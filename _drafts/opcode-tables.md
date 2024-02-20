---
title: Opcode Tables
excerpt_separator: <!--more-->
header:
  teaser: /assets/images/
  og_image: /assets/images/
categories:
  - Blog
tags:
  - blog/draft
tags:
  - M6800
published: false
synced: true
---
I made a website with opcode tables for some of my favorite 8-bit CPUs!

Why would I bother to do that?

There are lots of great [opcode tables](https://en.wikipedia.org/wiki/Opcode_table) out there for various 8-bit CPUs. [Pastraiser](https://pastraiser.com) is a well known website with lots of great tables (and I let myself be inspired by many aspects of them).

However, some of those tables contain minor errors. Not a big deal, mostly just cycles being off, but it still made me wish there were a collection of opcode tables on GitHub so anyone can help correct mistakes.

The tables I made are also not necessarily for CPUs, but for binary-compatible CPU families. That is, my Intel 8080/8085 table, for example, combines opcodes for both those CPUs since they're binary compatible.

When I recently made an emulator for the Intel 8080 Space Invaders and wrote some M6800 assembly for the DREAM 6800, I ran into a few issues with undocumented behavior.

For the Intel 8080, I discovered that [the Auxiliary Carry flag is affected by logical operations](https://retrocomputing.stackexchange.com/questions/14977/auxiliary-carry-and-the-intel-8080s-logical-instructions) (in strange ways), despite the manual saying they're not. Those flag behaviors were only documented in the 8085 manual.

And when I foolishly used an MC6803 assembler for my MC6800 code, [I discovered that it inserted a "Halt and Catch Fire" opcode into my program](choosing-the-wrong-m6800-assembler) against my will.

I haven't seen any CPU family opcode tables out there, so here was a chance to do something a little unique. I decided to merge the tables for the binary-compatible CPUs. This obviously posed a few usability problems, but I think it ended up okay, if information-dense.

I also tried to track down all illegal/undocumented opcodes. Easier said than done! [Many of the illegal 6800 opcodes were documented as early as in 1977](http://spivey.oriel.ox.ac.uk/wiki3/images/1/1a/Undoc6800.pdf), but many remain undocumented to this day (at least as far as I have found). I'll probably make a separate blog post about them later.

Another thing I wanted to do was present the table both in hexadecimal and octal. Many 8-bit CPUs' opcode tables make much more sense when viewed in an octal configuration; [see this great Intel 8085 example](http://www.righto.com/2013/02/8085-instruction-set-octal-table.html).

I also made opcode tables for CPUs that I haven't found tables for anywhere, such as the Fairchild F8 CPU, the F3850, and Pokémon Mini's CPU.
