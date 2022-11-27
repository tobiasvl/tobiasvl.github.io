---
title: "Link's Awakening 30th Anniversary"
excerpt_separator: "<!--more-->"
header: 
  teaser: /assets/images/
  og_image: /assets/images/
categories:
  - Blog
tags:
  - Game development
synced: true
published: false
---
{::comment}
#gamedev 
#devlog 
#blog/draft 
{:/comment}

Optional Heart Containers

This hack makes all Heart Containers dropped by bosses optional, by opening the door to the instrument when the boss is defeated rather than when the Heart Container is picked up. This is the way most modern Zelda games do it, including the Link's Awakening remake on Switch.

Link's Awakening forces you to pick up all Heart Containers, which makes for a fairly flat difficulty curve. In addition, Link's Awakening DX is even easier than the original, both in optional ways (the Color Dungeon reward, Medicine in chests, additional Owl Statue hints in dungeons) and in mandatory ways (the Genie boss is nerfed, and the Eagle boss can be cheesed with the Red Tunic). This hack introduces a way to make the game harder as well, to optionally balance it out a little better.

Note that the Heart Container will automatically despawn if you leave the boss room, and will be gone if you return. This is a limitation of the game; in fact, in the un-hacked version, if you defeat a boss and then leave the boss room without picking up the Heart Container (by falling into the pit in the Level 1 boss room, for example), the boss will respawn instead.

Changes:
- Defeating a boss will now open the door to the Siren's Instrument and play the corresponding music (and also still drop a Heart Container)
- Picking up a Siren's Instrument will now refill your hearts (a convenience in case you didn't pick up the Heart Container)




Many Zelda games let you organically set your own difficulty level. Link's Awakening is a fairly short game, and an easy one by most measures, but it's made easier by the fact that there are few ways to set your own difficulty level. You can avoid picking up Heart Pieces and Secret Seashells to not make the game super easy, but that's about it. In Link's Awakening DX, you can make the game even easier with the Color Dungeon. DX also makes the game easier for you, without giving you the option to avoid it, by giving more hints (which cannot be avoided because you can't tell the important hints from the overly helpful ones before reading them), more Medicine, making a boss weaker, etc.





This is an alteration of Link's Awakening DX to commemorate the 30th anniversary of the original Link's Awakening.

A mix of the old and the new, it aims to be a "definitive edition" of Link's Awakening, bringing together features from Link's Awakening (LA), Link's Awakening DX (DX), and Link's Awakening for Nintendo Switch (LANS) - and being compatible with many other hacks - in order to      create the ultimate Link's Awakening experience.

This hack does not make many major changes, but polishes and balances the game while restoring some original artistic and mechanical choices.

Changes (in rough order from major to minor):

- Optional Heart Containers: This is a backported feature from LANS, which makes it optional to pick up any of the Heart Containers dropped by bosses. This makes it possible to make the game harder (at the player's discretion).
- Scrolling Pause Screen: When playing on a Game Boy, the pause screen scrolls like in LA, instead of flashing like in DX. When playing on a Game Boy Color (or Advance), it still flashes to hide palette changes.
- Breakable Pots: Pots can now be broken by the L-2 sword and bombs. This also means that bombs cannot be acquired until after Bottle Grotto. This feature is from LANS.
- Change Tunic: After getting the red or blue tunic from the Color Dungeon, you can change back to the green tunic at any time from the pause menu. (Start -> Hold Select to bring up submenu -> A)
- Desaturate palette: You can change to the [url=https://www.romhacking.net/hacks/7202/]desaturated palette[/url] from the title screen (Select) or pause menu (Start -> Hold Select to bring up submenu -> B)
- Darker Rooms: Dark rooms in dungeons have been made darker than they were in DX, and more in line with LA.
- Uncensoring: The censoring related to the Mermaid and the Hippo has been removed. The English dialogue has been inspired by early text from a prototype of the English translation of LA: https://tcrf.net/Development:The_Legend_of_Zelda:_Link%27s_Awakening_(Game_Boy)/Text
- Skip Dialog: This restores the ability to skip certain text with the B button, which was present in Link's Awakening but removed in DX. In addition, more text in the game has been made skippable (such as the text when you pick up the compass).
- Improved Maneuverability: Shortcuts have been added, mostly taken from LANS. For example, you can now return from the Pothole Maze and Kanalet Castle quickly after solving them. A cave in the Kanalet Castle moat, which was cut from LA, has been reinstated as a shortcut up to    Manbo's cave, similar to a shortcut in LANS.
- Faster Genie: The boss in Bottle Grotto, the Genie, has had his faster speed from Link's Awakening restored, instead of being nerfed like in DX.
- No Medicine Pick-ups: The Medicine found in dungeon chests in DX have been removed. The Medicine can now only be found at Crazy Tracy's, like in LA.
- Fewer Owl Statues: Many of the extra Owl Statues in dungeons, which were added in DX and largely considered overly helpful, have been removed again.
- Restored Richard dialogue: Richard's dialogue when you accept his quest was made much less interesting in version 1.2 of Link's Awakening (which carried over to all versions of DX). The original dialogue is restored here.
- Split Key Cavern minimap: The minimap of Key Cavern (Level 3) is properly split into two floors again, like in LA and all non-English versions of DX.
- A couple of easter eggs have been added.

Compatible with:
- Link's Awakening DX v1.0, v1.1 and v1.2
(- Link's Awakening Redux (separate patch is provided, to be applied on top of Redux))
- Link's Awakening DX "GBA Enhanced"
- Super Game Boy Color Hack
- Quality of Life Improvements
- Link's Awakening DX Font Improvement
- Link's Awakening DX VWF version
- Photo Album Translation

Not compatible with:
- Link's Awakening Restoration (this hack includes the same changes, but in different ways)
- Link's Awakening Redux (this hack includes the same changes and/or is compatible with the hacks Redux includes)





SCROLLING PAUSE SCREEN:
- LA and LADX both use window to draw HUD and pause screen
- DX flashes the screen and draws entire window because of a palette change
- DX also rescrolls and redraws current room when closing window (why?) 