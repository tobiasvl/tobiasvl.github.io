---
title: "Choosing the wrong M6800 assembler"
excerpt_separator: "<!--more-->"
header: 
  teaser: /assets/images/m6800-logo.png
  og_image: /assets/images/m6800-logo.png
categories:
  - Blog
tags:
  - M6800
  - DREAM 6800
published: true
synced: true
---
{::comment}
#blog/published
{:/comment}

Using the wrong assembler can set your MC6800 on fire.
<!--more-->

This post is part of a series on writing an emulator for the DREAM 6800 computer. Read [the DREAM 6800 posts](/tags/#dream-6800) and look at [the emulator's repository](https://github.com/tobiasvl/drom).
{: .notice--info}

![M6800 logo](m6800-logo.png)

I've done a little homebrew for the Fairchild Channel F in the past, which was one of the earliest video game consoles. I'll probably write separate articles on that later. While developing for the Channel F's F8 CPU, I used a nice little assembler called [dasm](https://github.com/dasm-assembler/dasm).

So when I recently did some Motorola 6800 development, which you can read about in my article about [extending CHIPOS with more CHIP-8 instructions](/blog/chipos-hacking), I initially used dasm for that too. I knew dasm already, and it supports one of the M6800 processors, the MC6803. All right, I was actually targeting the MC6800, not MC6803. But what's the difference, really? I read that MC6803 is both opcode and binary code compatible with the MC6800, it just adds some 10 new instructions, but I just wouldn't use them. (Note to self: Write an article later which compares the different MPUs in the M6800 family!)

Halt and Catch Fire
-------------------

So I started developing my MC6800 program, assembling it using dasm in MC6803 mode, and running it in my MC6800 emulator. It worked fine, until I suddenly encountered a strange bug: My emulator would halt and catch fire!

Not literally, of course. You can read about the [Undocumented M6800 Instructions](http://spivey.oriel.ox.ac.uk/wiki3/images/1/1a/Undoc6800.pdf) in this article from _BYTE_ Magazine, or in the article [Investigating the HCF (Halt & Catch Fire) instruction on Motorola 6800](https://x86.fr/investigating-the-halt-and-catch-fire-instruction-on-motorola-6800/). Among these instructions is the so-called "Halt and Catch Fire" or `HCF`, with opcodes `$9D` and `$DD`.

_Halt and Catch Fire_ is also the name of an excellent and criminally underrated TV show. You should watch it!
{: .notice--success}

If the MC6800 executed one of these, it would do something strange: It would stop responding to interrupts ("halt") and start quickly reading each memory address in turn by incrementing the program counter ("catch fire"). The only way to recover would be to turn off the power! Oof.

When I started making my emulator, I naturally included the `HCF` instruction in my MPU implementation. I wanted it to be an accurate emulator; plus, `HCF` is a fun and mythical instruction! Except now my own program caused my own emulator to, well, halt and catch fire.

The culprit
-----------

In my program, I had the following line of assembly code:

    JSR $0040

In other words, "jump to the subroutine at address `$0040` in memory". Of course, I didn't hardcode the address, but had defined it earlier in the program. In MC6800 machine code, this should be assembled into the following byte sequence:

    BD 00 40

However, my assembled machine code was this instead:

    9D 40

And as I said earlier, `$9D` is the fabled `HCF` instruction.

The reason this happened is that while the MC6803, which dasm targets, is completely opcode compatible with the MC6800, that obviously only applies to the MC6800's _documented_ opcodes. The MC6803 also adds a few new opcodes.

I recently wrote an article about the [Motorola 6800 addressing modes](/blog/m6800-addressing-modes). The MC6800 has a few instructions that can operate on "zero page memory", ie. memory in the first 256 bytes of memory, or `$0000`–`$00FF`. Motorola calls this "direct addressing mode", while using two bytes to address any memory is called "extended addressing mode". Direct addressing is nice because it can take a single byte as an operand (the memory address), rather than two, and it's faster. However, `JSR` is not one of those instructions: It only provides extended addressing, so it always needs two bytes as an operand, even if the address is on the zero page.

Not so for the MC6803! It adds a very convenient `JSR` with direct addressing. As you might already have guessed, its opcode is `$9D`, replacing the MC6800's undocumented "Halt and Catch Fire" opcode. The dasm assembler will of course notice that I'm trying to jump to the zero page, use direct addressing, and make a little trap for the MC6800 MPU.

Picking another assembler
-------------------------

Some assemblers allow you to force a particular addressing mode, but dasm does not. I could continue to use dasm but hardcode the bytes in the assembly code, preferably with a macro, but that's still pretty hacky. I opened [an issue suggesting to add forced addressing modes](https://github.com/dasm-assembler/dasm/issues/41), and started looking at alternative assemblers.

* [crasm](http://htmlpreview.github.io/?https://github.com/colinbourassa/crasm/blob/master/crasm.html) is pretty standard in most Linux distributions, so I checked it out first, but it's a bit basic and lacks a few features I'd like.

* The CHIPOS code that can be found online, which I originally based my modifications on, is written for [asm68c](https://sourceforge.net/projects/asm68c/). It's a nice project, but not very modern or advanced. It doesn't support macros, for example.

* I also found [vasm](http://sun.hasenbraten.de/vasm/), which looks nice; it supports MC6800, and it can also force addressing modes (not that I needed it when it supported the 6800 natively, but I now know it's nice to have). However, its license doesn't allow commercial use. Not a problem for me in practice, but I prefer free software licenced projects when possible.

Finally I found [AS](http://john.ccac.rwth-aachen.de:8000/as/). It wasn't very easy to compile it for my Linux system, but once I got it working, I quickly found it was everything I wanted. It actually assembled CHIPOS out of the box (except that an equate called `TIME` had to be renamed, since AS has a pre-defined equate by that name) since it supports various syntaxes and settings.
