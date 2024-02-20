---
title: Emulating the MC6820 PIA
excerpt_separator: <!--more-->
categories:
  - Blog
tags:
  - blog/draft
tags:
  - M6800
  - DREAM 6800
  - Emulation
published: false
synced: true
---
When emulating various 8-bit computers, you might just stumble upon the Peripheral Interface Adapter (PIA).
<!--more-->

This post is part of a series on writing an emulator for the DREAM 6800 computer. Read [the DREAM 6800 posts](/tags/#dream-6800) and look at [the emulator's repository](https://github.com/tobiasvl/drom).
{: .notice--info}

I spent quite some time implementing the PIA in my emulator, reading through different documentation to figure out how it all worked, so I decided to write down some high-level details on how I ended up doing it. I'm almost surely getting some details wrong, so please comment if you find any inaccuracies.

PIA
---

Any Motorola M6800 system, including the DREAM 6800 which I'm currently emulating, is comprised of a bunch of modules. The most important one is the CPU, of course – or, as Motorola called it, the MPU – the MC6800. (M6800 is the family of microcontrollers, and MC6800 is the CPU microcontroller. Slightly confusing.) But another crucial component is the MC6820/MC6821, the Peripheral Interface Adapter, or PIA (which incidentally is also my mother's name).

The PIA was a versatile chip for connecting peripheral devices to systems, and appears in many other MC6800/MC6809-based systems such as the Apple I. In fact, it was so versatile that it was cloned by MOS Technology (as the MCS6520) and appears in many systems using the popular MOS 6502 CPU, such as the Atari 800 and Commodore PET.

Pinout to MPU
-------------

The PIA looks like this:









On the MPU side, the most important pins are these:

* Eight bidirectional data lines (D0–D7), which send data to and from the MPU
* Two lines (IRQA and IRQB) that send interrupt requests to the MPU

On real hardware, the chip select (CS) lines are used to select which chip, A or B, the MPU accesses. The register select (RS) are used to select registers. In most systems, these lines will be hooked up to the address bus, so we can abstract this away. On the DREAM 6800, for example, address `$8010` selects chip A and the control register, `$8011` selects chip A's output/DDR register, and `$8012`/`$8013` do the same for chip B. And the enable (E) and read/write (R/W) pins don't need to be implemented in my emulator, at least.

Split personality
-----------------

The PIA is split in two parts, A and B. Each of these two chips has:

* Eight bidirectional I/O peripheral data pins (P0–P7) that connect the peripheral device to the PIA
* Two control pins (C1 and C2) that might set the interrupt flags in the PIA, among other things
* One interrupt request line (IRQ), previously mentioned

In addition, each chip has three internal registers:

* A control register that configures the PIA
* A data direction register (DDR) that selects the I/O pins for input or output
* An output register (also called data register) that contains the output data

PIA A and PIA B are almost identical, but differ slightly in how they handle interrupts.

On the DREAM 6800, PIA A is dedicated to the keypad. PIA B is hooked up to the tape drive and the speaker.

Data direction
--------------

Peripheral devices can both provide input to the PIA, or receive output from it. The way this is done is quite ingenious, although it took me a while to understand how it all functions.

The data direction register (DDR) of each chip functions as a mask of sorts. The register is eight bits. Each bit tells the PIA whether the corresponding peripheral data pin (P0–P7) is used for input or output: A 0 bit in the DDR means the corresponding P pin is used for input, while a 1 bit means the pin is output.

Output
------

If the MPU outputs data to the PIA through the data (D) lines, by writing to `$8011` or `$8013` in the DREAM's case, this data is stored in the PIA chip's output register. This register functions as a cache. The output register's data is then continuously output to the peripheral (P) lines that are set to be output in the DDR.


TODO: What do the others output

Input
-----

If a peripheral (P) pin is set to be an input pin in the DDR, and the MPU reads from the corresponding data (D) pin, it will receive the raw input from the peripheral device. It passes through directly, and is not stored in the output register. This might seem evident from the register's name, but it's also sometimes called "data register", which confused me initially.

If a pin is set to be an output pin in the DDR, what the MPU will read from the peripheral pin actually depends on the PIA chip. For PIA A, it will most likely read a 0 (although you can't rely on it; it depends on the peripheral pin's voltage). For PIA B, it will read from the output register!

In addition, when the MPU reads from the data (D) lines, the corresponding PIA chip's IRQ statuses (1 and 2) in the control register will be cleared. Reading data from the PIA is considered acknowledging the interrupt request.

Interrupts
----------

Since the IRQ statuses in the PIA's control register will be cleared when the data (D) lines are read, as I just mentioned, a usual setup is that the peripheral will input data to the peripheral (P) pins and also to a control pin (C1 or C2) in order to interrupt the MPU. The MPU's interrupt handler reads the input from the PIA, and the IRQ status is automatically cleared.

This clashed a bit with my emulator's memory viewer. While implementing the PIA and debugging it, I made constant use of the memory viewer to see what data the data lines of the PIA held. But the memory viewer reset the IRQ when reading, so the MPU never had time to receive the IRQ. Oops. Also, it took a while for me to understand how to combine the DDR, output register, peripheral and data lines to view the exact data that the MPU and the peripheral would see on its respective pins.

Further reading
---------------

* http://www.coinopcauldron.com/piaarticle.html
