---
title: "Emulating the Space Invaders look and feel"
excerpt_separator: "<!--more-->"
header: 
  teaser: /assets/images/moon_invaders.png
  og_image: /assets/images/moon_invaders.png
categories:
  - Blog
tags:
  - Emulation
---

My attempt at making a _Space Invaders_ emulator look like the arcade cabinet.

<!--more-->

Check out the [the emulator's repository](https://github.com/tobiasvl/moon-invaders) if you're interested..
{: .notice--info}

To me, emulation isn't just making a program that accurately performs the same computations that the original hardware did; it's also important to me that it captures the feeling of the original game.

A popular first project for emulator developers is the original Taito/Midway _Space Invaders_ arcade machine from 1978. The hardware isn't very hard to emulate (except for the sound chip, which is usually not emulated; usually prerecorded sound files are played instead).

But _Space Invaders_ is more than its CPU. Inside the arcade cabinet, there's a CRT monitor. Interestingly enough, it's rotated 90 degrees counter-clockwise, as an early example of a "landscape mode" game. The monitor isn't actually visible, but there's a mirror in front of you that superimposes the light from the monitor onto illuminated background artwork. The game is in black and white, but strips of colored gel is adhered to the monitor to give some game elements color.

These elements give a look and feel that's missing from most basic _Space Invaders_ emulators out there. Let's go through the elements.

Aspect ratio
------------

In most _Space Invaders_ emulators, one bit of graphical information is drawn as one pixel on the screen.

![](/assets/images/space_invaders_taito_cop.png)

However, CRT monitors don't have "pixels" in the same sense. The electron beam is turned on for a period of time as it sweeps horizontally from left to right, illuminating parts of a scanline, before it's turned off again.

Usually, this means that one CRT "pixel" is slightly wider than it's tall. The height is one scanline, which can be translated into one pixel, but the width can be more than that. That's the case for _Space Invaders_. Because its resolution of 256 x 224 isn't quite 4:3 like the monitor, the image gets stretched vertically as each pixel occupies a little more of the horizontal scanline.

Note that since the CRT monitor in _Space Invaders_ is rotated, one pixel is in fact slightly _taller than wide_ instead, after rotation. This means that the iconic crab-like space invader looks too squat and flat in many emulators, for example. The enemies should in fact look more square.

Scanlines
---------

Since _Space Invaders_ uses an old CRT monitor, the scanlines are actually visible. Because of the phosphorescent glow, they're not easily discernible, but the gel overlay mutes the glow on the colored areas, making them stand out more there.

{% include figure image_path="/assets/images/space_invaders_scanlines.png" caption="Screenshot from [this video](https://www.youtube.com/watch?v=DJMr36hLY38)" %}

This is a common visual effect, which many emulators provide. However, they often forget that the monitor in the cabinet is rotated. The scanline effect should obviously be applied before rotating, which would produce horizontal scanlines rather than vertical.

In addition, the scanlines should only affect the image on the actual CRT monitor. This is projected onto artwork of a desolate moon. Some emulators apply the scanline effect to this image as well, which is incorrect.

Gel overlay colors
------------------

Most _Space Invaders_ emulators use bright, primary colors for its gel overlay, like shown above. The overlay is placed like this (image from [The Cutting Room Floor](https://tcrf.net/Space_Invaders_(Arcade)):

![](/assets/images/space_invaders_overlay.png)

However, when looking at some YouTube videos ([1](https://www.youtube.com/watch?v=DJMr36hLY38), [2](https://www.youtube.com/watch?v=1uSzmzZP1s8)) with footage of actual arcade gameplay, several things stand out to me about this color scheme:

<figure class="half">
    <a href="/assets/images/space_invaders_taito.png"><img src="/assets/images/space_invaders_taito.png"></a>
    <a href="/assets/images/space_invaders_midway.png"><img src="/assets/images/space_invaders_midway.png"></a>
    <figcaption><a href="https://www.youtube.com/watch?v=DJMr36hLY38">Taito cabinet</a> on the left, <a href="https://www.youtube.com/watch?v=1uSzmzZP1s8">Midway cabinet</a> on the right</figcaption>
</figure>

* The white should have a bluish tint from the CRT phosphorescence (intensity of the blue varies)
* The green should look a little "washed out"
* The red isn't actually red, but either magenta (Taito cabinet) or orange (Midway cabinet)

It's also interesting to note the color of the dividing line between the playing area and the "HUD" with lives and credits. In the videos shown above, parts of the line is green where the overlay extends down to the number of lives, but the length of that extension varies. It seems that the Midway's line is too short (it would only cover three lives with green, making any additional lives white), but at any rate, it's obvious that there are several different overlays.

I haven't found too many photos of the actual overlays out there, but here's an interesting one:

{% include figure image_path="/assets/images/space_invaders_gel.jpg" caption="Image from [BrentRadio's Space Invader page](http://www.brentradio.com/SpaceInvaders.htm)" %}

This one is also too short to color more than three lives, as in the Midway video, but the interesting part is that there's a one-scanline gap between the two green areas, so that the dividing line stays white. (In addition, the number denoting the number of lives would be white here, not green like in those videos.) I don't know if all the overlays had this gap and that people just misaligned it when applying it to the screen, but seeing as there were different overlays in the wild, perhaps some were just solid green blocks without the gap.

At any rate, in my emulator I decided to go for a gel overlay with a gap for the dividing line, since that clearly existed, and I also decided to apply it perfectly to my digital CRT screen.

Lighting
--------

Not too much to say here. The CRT's phosphorescent glow can be added with a simple glow/light bloom shader. In the one I used, a `min_luma` value of 0.4 (from 0 to 1) and a strength of 5 (the shader's default) gives a satisfying glow that blows out the scanlines in the invaders, but is less prominent in the colored areas, just like the case is in the cabinet (the colored gel overlays mask the glow somewhat).

The background of the moon is lit from within the cabinet. That's harder to emulate, but the image I found online of the Taito cabinet's moon was very vibrant, so I added a vignette shader that adds a little shadow to the corner, for a more dynamic lighting look.

The more muted moon in the Midway cabinet would need a different lighting shader to look natural. the only image I've found of it online is very dark; if anyone has a better one than MAME (unlikely) let me know!

Sound
-----

Like I said at the start, the sound chip is pretty hard to emulate, and most people don't bother. I'd like to do it, but I haven't yet. So I just make my emulator play prerecorded sound effects at the right times. These sound effects can readily be downloaded online.

At least most of them can. There are 9 sound effects that can be found online, but the game actually has 10 sound effects. I haven't been able to find the last one, which plays when you gain a new life (by scoring either 1000 or 1500 points). [The sound effect can be heard at 2:04 in this video](https://youtu.be/DJMr36hLY38?t=124). I'm not sure why I can't find it online, but if anyone has it, I'd be much obliged.

How to emulate
--------------

I emulate all this in [my _Space Invaders_ emulator](https://github.com/tobiasvl/moon-invaders) in this order:

1. First draw the moon backdrop image, with a vignette shader to make it look illuminated
2. Draw the game's VRAM to a stencil canvas, where each "pixel" is a rectangle 4 pixels wide by 3 pixels tall
3. Apply the "gel overlay" image (where the non-overlayed parts of the image are white) to all the stenciled pixels, and draw it to a new canvas
4. Apply a scanline effect shader to this canvas, then a barreling distortion and glow effect shader, in that order
5. Rotate the canvas 90 degrees counterclockwise and draw it on top of the backdrop

The result is (as I intended) a fairly close match to the Taito cabinet as shown above, mixed with the Midway cabinet (a subtler bloom, for example):

<figure class="half">
    <a href="/assets/images/space_invaders_taito_2.png"><img src="/assets/images/space_invaders_taito_2.png"></a>
    <a href="/assets/images/moon_invaders.png"><img src="/assets/images/moon_invaders.png"></a>
    <figcaption>Taito Space Invaders on the left, my emulator on the right</figcaption>
</figure>

Of course, I could always do more here. For example, the background doesn't really look like it consists of two parts, where one is illuminated from below, like in the cabinet. But I'm satisfied for now!
