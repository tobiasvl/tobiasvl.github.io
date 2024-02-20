---
title: Mini Lights Out for CHIP-8
excerpt_separator: <!--more-->
header:
  teaser: /assets/images/mini-lights-out.gif
  og_image: /assets/images/mini-lights-out.gif
categories:
  - Blog
tags:
  - blog/published
  - gamedev
  - devlog
tags:
  - CHIP-8
  - Game development
published: true
synced: true
---
I've always thought games are shaped by their medium, so ever since I discovered [CHIP-8](https://en.wikipedia.org/wiki/CHIP-8) I've wanted to make a game that's very idiomatic.<!--more--> [Octojam](https://itch.io/jam/octojam-6) was my chance.

CHIP-8 has a very distinctive human–computer interface: Input is a hexadecimal keyboard on a 4x4 grid, and output is done by XOR-ing pixels on the screen. How could I use that to create a simple game within CHIP-8's other constraints, which felt like a natural fit for the platform?

XOR pixels
----------

What really intrigues me about CHIP-8 is its method of drawing to the screen. The color palette is monochrome; each pixel is 1 bit, and so there are only two colors. When drawing something (a CHIP-8 "sprite") on the screen, the affected pixels are flipped from off to on. Or, if there already was something drawn in that position, from on to off – if this happens, an internal flag register is set. So the bytes that are on the screen are XOR-ed with the bytes in the sprite, and you can use the flag for sprite collision!

This is one of the most elegant graphics systems I've ever heard of, so I wanted to make something inspired by it. For inspiration, I also looked at [Inktober](https://inktober.com)'s themes this year (which several game jams and gaming communities are using for inspiration too), and landed on "Dark" and "Pattern".

Lights Out Classic
------------------

Lights Out was an electronic toy made by Tiger Toys in 1995. It was a handheld device with a grid of 5x5 buttons, each with a light that was either on or off. Pressing a button toggled its light, as well as the lights of the four adjacent buttons above, below, to the left and right (if applicable; this classic version did not wrap around the grid's edges). The game was won if you managed to turn off all the lights.

A lot of people have written [mathematical proofs](https://www.jaapsch.net/puzzles/lights.htm) concerning this game. Not all initial light configurations can be solved in the classic version, so it came with a set of hand made puzzles that were all solvable. This wasn't very appealing to me, as that meant I'd have to store a bunch of puzzles and write routines to unpack them and display them on the screen. So I started looking at other variations of the game.

I'm not sure when Mini Lights Out was released, but as its name implies, it's a smaller version of Lights Out Classic: 4x4 lights. This seemed perfect to me, as CHIP-8 has a 16-key hexadecimal keyboard arranged in a 4x4 grid! My adaptation wouldn't need a cursor that moved around or anything, each key could directly map to one light button. How elegant!

Mini Lights Out also made one other change to how the game played: Pressing a button here always toggled its own light and four other lights. The grid functioned like a torus, essentially, wrapping around on the edges. This change, according to the math wizards of the Internet, meant that all configurations were winnable. I could randomly generate puzzles!

VIPER
-----

During the late 70s and early 80s, a computer magazine called VIPER was dedicated to the COSMAC VIP – the original computer that shipped with CHIP-8 on it. I read all issues of it, and it really made me nostalgic in a weird way for this era of computing that I never partook in. So I decided to pretend like my game was featured in an issue of VIPER, and created a PDF "manual" for the game, complete with a complete code listing in hexadecimal, so subscribers of the magazine could painstakingly input the source code on their VIP to play. [Download Mini Lights Out's manual here.](https://github.com/tobiasvl/mini-lights-out/raw/master/mini-lights-out.pdf)

Making the game
---------------

CHIP-8 uses a machine-like language which looks like an assembly language, although it has in fact always been an interpreted language – today we'd call it a bytecode virtual machine. I've dabbled in console homebrew before and am familiar with assembly, but [Octo](http://johnearnest.github.io/Octo/) provides a high-level language that "assembles" to CHIP-8 bytecode, so it actually felt like I was writing in a fairly modern language.

All in all this was a great experience, and I'm already looking forward to Octojam 7 in 2020.
