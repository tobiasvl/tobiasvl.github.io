---
title: Chipolarium
excerpt_separator: <!--more-->
header:
  teaser: /assets/images/dream_6800_ea.jpg
  og_image: /assets/images/dream_6800_ea.jpg
categories:
  - Blog
tags:
  - blog/draft
  - gamedev
  - devlog
tags:
  - CHIP-8
  - Game development
  - Polarium
published: false
synced: true
---
For this year's [Octojam](https://octojam.com), I made a CHIP-8 demake of _Polarium_.

<!--more-->

https://tobiasvl.itch.io/chipolarium

PLAY CHIPOLARIUM HERE

VIDEO

Quotes from Octojam video

CHIP-8 uses an interesting "pixel toggle" drawing technique, so I've made a habit of demaking puzzle games based on toggling binary states: [Mini Lights Out](https://tobiasvl.itch.io/mini-lights-out), [Think-a-Dot](https://tobiasvl.itch.io/flip-8), and to some extent [Turm8](https://tobiasvl.itch.io/turm8). This year I picked one of my favorite puzzle games.

_Polarium _is a 2004 game for Nintendo DS that involves drawing a line on your DS screen with a stylus, in order to flip tiles from black to white or vice versa. Sounded easy enough to implement in CHIP-8, especially since I've already done it once before ([Picolarium](https://tobiasvl.itch.io/picolarium) for PICO-8). This game has been remade before on Atari 2600, so I figured it deserved a CHIP-8 port as well.

Since Polarium puzzles can be 10x10 tiles in size (including a border that can be traversed), I decided to target Super-CHIP for this game for the increased 128x64 resolution. I also happen to own two [HP 48 calculators that can run Super-CHIP games natively](https://tobiasvl.github.io/blog/chip-8-hp-48/).

## Title screen

I like a good title screen. The original _Polarium_ has a nice animation, where the stylish Polarium logo, white on black, is slowly inverted one tile at a time to black on white.

GIF OF POLARIUM

I decided to call my game Chipolarium, crammed that long title onto the title screen (squeezing the letters a little), and set out to find a good way to do the screen animation.

Slowly inverting the screen is obviously super easy on the CHIP-8: You just put a solid sprite consisting of all 1 bits anywhere, and that area will be inverted. The original Polarium simply inverts the screen in a deterministic order, so I could just do that here too.

Luckily I stumbled upon [this great blog post](http://fabiensanglard.net/fizzlefade/index.php) about the screen transition in Wolfenstein 3D called "FizzleFade".

GIF OF WOLFENSTEIN

Upon death, the entire screen is turned red one pixel at a time in a random order, with no pixel collisions! Amazing! The technique uses a linear-feedback shift register to achieve this, and the article provides C code that was very simple to adapt to CHIP-8.

GIF OF CHIPOLARIUM

## Gameplay

The original _Polarium_ was a Nintendo DS game, where you used the stylus to draw the line. However, the sequel _Polarium Advance_ on the GBA showed that it was just as fun to draw it with a D-pad, 

## User interface

ITERATIONS

LEVEL SELECT

## HP 48 compatibility

I wanted to be able to run this game on my HP 48 calculator.
