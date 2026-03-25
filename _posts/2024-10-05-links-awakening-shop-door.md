---
title: Restoring a locked door in The Legend of Zelda - Link's Awakening
excerpt_separator: <!--more-->
image: /assets/img/
categories:
  - Blog
tags:
  - Game Boy
  - ROM hacking
published: false
synced: true
---
Bringing back a discarded feature in Game Boy assembly code, step by step.

<!--more-->

> [!WARNING] Flashing lights
> WARNING: This article contains animated GIFs that may potentially trigger seizures for people with photosensitive epilepsy. Viewer discretion is advised.

One of my pastimes is contributing to [the disassembly of _The Legend of Zelda - Link's Awakening DX_](https://github.com/zladx/LADX-Disassembly/) (my favorite game). Doing digital archeology in that ~30 year old codebase sometimes uncovers bugs and unimplemented features. I thought it'd be interesting to look at one of those unimplemented features and restore it, to give a small peek into the code. It's a minor feature, but this article is pretty verbose, so you can code along with me if you want to!

As everyone who has played the game knows, there's a shop in the starting area, Mabe Village. Famously, it's possible to steal from this shop if you're fast enough, which is a good way to nab the Bow & Arrows (usually sold at 980 Rupees). If you return to the Shop after having stolen something, the shopkeeper will stand in the middle of the room and kill you by shooting a laser at you.

![zelda_stock_shop](../../assets/img/zelda_stock_shop.gif)

As you can see above, Link enters the shop and stands still while he lets the shopkeeper kill him, even though the door is wide open behind him. [You would think he'd just run away](https://www.zerochan.net/full/2702032) - but interestingly, the code reveals that at some point early in development, this encounter was meant to close the door behind Link, as if you'd just entered a boss room!

Let's try to figure out why this doesn't happen in the game. The following annotation is from the [the disassembly](https://github.com/zladx/LADX-Disassembly/blob/008d015/src/code/bank0.asm#L7214-L7251), documented by [@kemenaran](https://github.com/kemenaran):

>  You can find the corresponding machine code (or "hex code" as ROM hackers sometimes call it) in your own copy of the game's ROM, in the memory location `$37B6` in bank 0 (at least in version 1.0 of the North American version of the ROM). The disassembly has comments on all lines of code with the corresponding bank number and memory address, but I've omitted those here for brevity.
{: .prompt-info }

```asm
LoadObject_IndoorEntrance::
    ; This code is supposed to replace the Shop's indoor entrance
    ; by a closed door if Link has stolen from the shop
    ; (presumably for dramatic effect).
    ;
    ; However it doesn't work, because:
    ; 1. the Shop's room was moved from $D3 to $A1, but the code was never updated,
    ; 2. even when fixing the room id, the closed door has garbled tiles.
    ;
    ; In the final version, this code is never triggered, because the
    ; $D3 room (Kanalet's Castle main entrance) doesn't have a
    ; IndoorEntrance object at all.
    
    ; If on an Indoor B map…
    ldh  a, [hMapId]
    cp   MAP_INDOORS_B_END
    jr   nc, .end
    cp   MAP_INDOORS_B_START
    jr   c, .end
    
    ; … and in Kanalet main entrance room (probably used to be the Shop's room)…
    ldh  a, [hMapRoom]
    cp   ROOM_INDOOR_B_KANALET_MAIN_ENTRANCE ; $D3
    jr   nz, .end
    
    ; … and has stolen from shop…
    ld   a, [wHasStolenFromShop]
    and  a
    jr   z, .end
    
    ; … load a closed entrance instead of a open one.
    jp   LoadObject_ClosedDoorBottom
.end
```

To summarize the code above: When a regular door entrance is loaded, this code checks if we're in a specific room and whether we've stolen from the shop, and if so, it should load a closed door instead.

The code does nothing in the final game, because it checks the wrong `hMapRoom` ID, the internal identifier for the current screen you're on.

> One interesting thing to note is that this defunct piece of code is run every time the `LoadObject_IndoorEntrance` subroutine is called, which is every time you enter a screen containing an `OBJECT_INDOOR_ENTRANCE`, which is every house in the game!
{: .prompt-tip }

At first glance, this seems easy enough to fix - just update the room ID from `$D3` to `$A1`, which is the actual ID for the shop interior. The comment in the code says this garbles the tiles around the door, but let's see what actually happens.

![zelda_fixed_room_number](../../assets/img/zelda_fixed_room_number.png)

It looks a little garbled, maybe, but it actually just has the wrong colors. What happens when we check for the correct room ID is that it replaces the original entrance object with the "closing door", which unlike the entrance just has regular walls on either side of the door opening (instead of those logs or poles). Those wall tiles still have the same color palette as the original entrance object, though, so the walls are orange instead of white. Since the tiles look OK, let's leave the palette issue on the backburner for now.

A more important question: Why doesn't the door close? There are two reasons for this, which can be discovered and fixed in any order, but both require fairly deep dives into the code to be found, and I won't show all that here. Luckily, if you've played the game extensively, you can realize what the problems are from experience.

The first reason is that the actual closing of the door only triggers once Link has moved past the doorway (otherwise the door would clip into him). Usually for regular rooms with closing doors, the door closes when Link has moved manually past the threshold. In boss rooms, Link is scripted to automatically move far enough into the room to be clear of the closing door (although some rooms close the door ). Link is scripted to move into the room in the shopkeeper encounter too, but it doesn't move him far enough before the dialog box pops up, so he simply never clears the doorway.

Okay, so maybe we can just increase the number of pixels that Link moves? Sounds simple enough!

As we saw above, the code that's responsible for stuff like closing doorways is connected to the room code. However, any scripts that handle moving Link in specific ways in specific encounters are connected to "entities", like bosses.

So the script that moves Link upwards when he enters the shop is specific to the shop. To locate it, you can search the codebase for anything that's likely to show up in this encounter, like:
- `wHasStolenFromShop`, the flag that says whether you've stolen from the shop or not, which is likely to be checked to see if the script should run 
- `Dialog038`, the "I wasn't kidding when I said pay!" dialog text, which appears when Link has stopped moving

The stalwart detective will find [this relevant code](https://github.com/zladx/LADX-Disassembly/blob/008d0/src/code/entities/04_shop.asm#L161-L175):

```asm
    ldh  a, [hLinkPositionY]
    sub  $01
    ldh  [hLinkPositionY], a
    cp   $74
    jr   nz, .ret_7838

    call_open_dialog Dialog038
    ld   hl, wEntitiesStateTable
    add  hl, bc
    ld   [hl], $04
    ld   a, MUSIC_BOSS
    ld   [wMusicTrackToPlay], a

.ret_7838
    ret
```

In English: Every frame, check if Link's vertical Y position is `$74` (or 116): if not, move him 1 pixel up (subtract 1 from his Y position) and return; otherwise, open the dialog box and start playing the boss music.

With trial and error, we can try changing the `$74` value to whatever smaller Y value we want, but all values will fail at this point - the door doesn't close no matter how far into the room we move.

![Pasted image 20241021141738](../../assets/img/Pasted%20image%2020241021141738.png)

Here we come to the second requirement. Again, it helps if you're familiar with the game, because then you probably know this: When you enter a room in a dungeon that normally closes the door behind you, but you've already solved that room's puzzle earlier, the door doesn't close after all and the "puzzle solved" jingle plays. Doors like this always start out open, and then close only if necessary. Or: We've spawned a door that *is able to close*, but we apparently also need to tell it to close.

Recall that the original code calls `LoadObject_ClosedDoorBottom` to replace the entrance with the door object, so let's check it out.

```asm
LoadObject_ClosedDoorBottom::
    ld   e, $05
    call func_373F
    ld   a, [wC18A]
    or   $02
    ld   [wC18A], a
    ld   [wC18B], a
    jp   LoadObject_OpenDoorBottom
```

Okay, that piece of code isn't annotated yet (maybe we could do that with the findings from this investigation), but it calls some function, manipulates some values (probably checking the "is the puzzle solved" condition), and then jumps to `LoadObject_OpenDoorBottom`. This fits the behavior we're used to.

```asm
LoadObject_OpenDoorBottom::
    ld   a, ROOM_STATUS_DOOR_OPEN_DOWN
    call UpdateIndoorRoomStatus
    push bc
    call label_35EE
    ld   bc, data_37E1
    ld   de, data_36E8
    jp   Func_354B
```

To dig deeper, we need to find the code that actually closes the door. 

TODO MORE INFO

It turns out that for the door to actually close, there needs to be a trigger registered to the room that tells the game when to open the door.

To fix it we can register a dummy event that will never be true, because we don't need the door to ever open again. Let's just say that to trigger the effect of opening all locked doors in the room, you must kill the enemies (of which there are none) in the room in order:

```
    ld   a, EFFECT_OPEN_LOCKED_DOORS | TRIGGER_KILL_IN_ORDER
    ld   [wRoomEvent], a
```

>[!NOTE]
>I could've chosen any trigger here, but I'm fascinated by the `TRIGGER_KILL_IN_ORDER` one - it's the trigger that's only used in one single room in the entire game, in Bottle Grotto ("First, defeat the imprisoned Pols Voice, Last, Stalfos..."), and which is the cause for the [Tunic Glitch](https://zelda.fandom.com/wiki/Glitches_in_Link%27s_Awakening#Obtaining_Colored_Tunics_Without_Visiting_the_Color_Dungeon_(Including_Extra_%22Damage_Sprite_Tunic%22)) because the game actually records the order of every enemy you kill in every room in the game!

Back to the trial and error of placing Link. It turns out the maximum value required for Link to properly clear the door *and* give it time to play its closing animation before the dialog pops up is `$69`, or 105, so we'll use that value.

Finally! The door ceremoniously closes behind Link. Our goal is achieved!

![zelda_displaced_laser 1](../../assets/img/zelda_displaced_laser%201.gif)

Oh, wait... Now that Link has moved too far into the room, the shopkeeper's laser beam doesn't hit him dead on anymore, since its length is hardcoded. I guess that's why Link's end position was hardcoded to `$74` to begin with...

We could shorten the beam, but it consists of two identical lightning bolt sprites, so we'd probably have to remove one whole 16-pixel tall bolt and that would mean moving Link even further into the room and ugh.

Instead, I got an idea: We could script Link to walk far enough into the room for the closing of the door to trigger, and then move him *backwards* far enough to line up with the laser beam again! I'm sure there are more elegant ways to do this, but this is what I ended up with, which replaces the code snippet above which originally moved Link to Y coordinate `$74`:

```asm
    ; WRAM address $D1C3 is unused, so we use it as scratch to store Link's current state
    ld   a, [$D1C3]
    and  a
    jr   z, .moveUp
    cp   $20
    jr   z, .moveDown
    jr   c, .hesitate
    ret
	
.moveUp
	; Move Link up until he triggers the door
    ldh  a, [hLinkPositionY]
    dec  a
    ldh  [hLinkPositionY], a
    cp   $69 ; At this point the door is triggered
    ret  nz
	
.hesitate
	; Make Link linger at the top position a little before he backs away
    ld   a, [$D1C3]
    inc  a
    ld   [$D1C3], a
    ret
	
.moveDown
	; Move Link back towards the door again
    ldh  a, [hLinkPositionY]
    inc  a
    ldh  [hLinkPositionY], a
    cp   $72 ; Link's final position
    ret  nz
	
    call_open_dialog Dialog038 ; Trigger the shopkeeper's dialog
```

> I changed Link's final Y position from `$74` to `$72`, because it avoids any clipping with the door, and because I actually think that lines him up even better with the laser blast than in the original game.
{: .prompt-tip }

 This is fairly straight forward, and I think it makes for a nice effect: Link enters the shop without a care in the world, but upon seeing the shopkeeper, he retreats instinctively only to back into the closed door. Boom!

![zelda_wrong_palette](../../assets/img/zelda_wrong_palette.gif)

And then all that remains is to fix the original palette issues with the door.




Find out that the closed door is OBJECT_CLOSED_DOOR_BOTTOM, $F1

The room is A1, located in the indoors B map

So we find BGAttributesPointers_IndoorsB in [LADX-Disassembly/src/data/object\_attributes/pointers.asm at 008d01541f8cab3f4590cbc94a690af2b9a7979f · zladx/LADX-Disassembly · GitHub](https://github.com/zladx/LADX-Disassembly/blob/008d01541f8cab3f4590cbc94a690af2b9a7979f/src/data/object_attributes/pointers.asm#L521-L523)

It's indexed by room ID, so we scroll down to A1 and see that it uses IndoorsBObjectsAttrmap_00 [LADX-Disassembly/src/data/object\_attributes/pointers.asm at 008d01541f8cab3f4590cbc94a690af2b9a7979f · zladx/LADX-Disassembly · GitHub](https://github.com/zladx/LADX-Disassembly/blob/008d01541f8cab3f4590cbc94a690af2b9a7979f/src/data/object_attributes/pointers.asm#L685)

Basically all the rooms use that attribute map, except a few

Those attribute maps are stored in this file [LADX-Disassembly/src/data/object\_attributes/indoors\_b.asm at 008d01541f8cab3f4590cbc94a690af2b9a7979f · zladx/LADX-Disassembly · GitHub](https://github.com/zladx/LADX-Disassembly/blob/008d01541f8cab3f4590cbc94a690af2b9a7979f/src/data/object_attributes/indoors_b.asm)

IndoorsBObjectsAttrmap_00 is at the top of the file. Each list is indexed by object ID, and then each object has four attributes, one for each tile. So we scroll down to F1 and see its attributes [LADX-Disassembly/src/data/object\_attributes/indoors\_b.asm at 008d01541f8cab3f4590cbc94a690af2b9a7979f · zladx/LADX-Disassembly · GitHub](https://github.com/zladx/LADX-Disassembly/blob/008d01541f8cab3f4590cbc94a690af2b9a7979f/src/data/object_attributes/indoors_b.asm#L249)

They're 01, 01, 01, 01. This means that all four tiles have palette number 1. But that's not correct, in the finished game it has palette 00. We can also check OBJECT_INDOOR_ENTRANCE, the regular door opening in the shop, which has object ID FD. That should have 05, 05, 05, 05, but that's also clearly incorrect.

So we're somehow not looking at the correct attribute map. It turns out this is a place where the disassembly is a little lacking in documentation.


But wait. The door objects aren't just four tiles. We know this because the regular open door has those two poles on either side of it, and because the palette for the closed door also covers these tiles. So it's eight tiles. But objects only have four attributes?

If we look at [LADX-Disassembly/src/data/objects\_tilemaps/indoor.dmg.asm at 008d01541f8cab3f4590cbc94a690af2b9a7979f · zladx/LADX-Disassembly · GitHub](https://github.com/zladx/LADX-Disassembly/blob/008d01541f8cab3f4590cbc94a690af2b9a7979f/src/data/objects_tilemaps/indoor.dmg.asm) we see that it only has tilemaps for objects 00 through EB. The regular entrance has ID FD, and the closed door has F1. So where are EC through FF?

Those are handled differently, and in fact we could have discovered this earlier. Remember LoadObject_ClosedDoorBottom from earlier? It's part of a jump table that contains the missing IDs from EC to FD (FE and FF aren't used apparently).

These objects are "macro objects" that expand to two objects, one left side and one right side.

LINK TO LoadRoomObject HERE TODO

Finally we find the actual objects: The "open door" is 8C (left) and 08 (right), and when it's closed it's 37 and 38.










Just for fun, there are other, hackier ways we could have solved this too, if we were ROM hackers working without a disassembly and a deep knowledge of the codebase:

We could simply busy-wait until the VBlank period and set the palettes manually. This is the simplest way, is very hacky, and means that the game's regular game loop would be suspended for a frame.

```
	di
	
```

A less hackier version of the above would be to use the game's built-in graphics routines and queue up the palettes to be drawn by the game loop. This is basically a manual version of what we ended up doing.

Hacky way:


If you don't know how they work, [Pan Docs explain Game Boy Color palettes](https://gbdev.io/pandocs/Palettes.html#lcd-color-palettes-cgb-only) concisely.

Like most other graphics manipulation on the Game Boy, we need to change the palettes 

As a quick and dirty hack, we just queue the correct tilemap attributes (`$04`, to select palette number 4) for the correct tiles (`$99E8` and `$99EB`) to be drawn during the next available time. This is the "correct" way to do it, but it feels overkill, especially in how many bytes this routine takes up; we could just busy-wait until the vertical blanking period instead and copy the bytes over directly, but that feels even uglier! 

```
FixShopDoorPalette::
    push bc
    ld bc, .doorPalette
    ld hl, wDrawCommandAlt
    ld a, [wDrawCommandsAltSize]
    ld e, a
    ld d, $00
    add hl, de
    ld e, .end - .doorPalette
:   ld a, [bc]
    ld [hli], a
    inc bc
    dec e
    jr nz, :-
    pop bc
    ret

.doorPalette:
    db $99, $C9, $41, $01, $99, $E8, $03, $04, $01, $01, $04, $00
.end
```

IMAGE OF FINAL FINAL

After all this, I guess we can conclude that this is less of a bug, and more of a feature that they abandoned implementing pretty early on. Considering how much was missing for this to work, the oversight isn't really that it was left out, but that they forgot to remove some code they must have known was non-functional.

The developers of the remake also either didn't know about this, or they also chose not to implement it, because the door is wide open in that version too.

![zelda_shop_remake](../../assets/img/zelda_shop_remake.png)
