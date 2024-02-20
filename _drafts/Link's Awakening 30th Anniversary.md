---
title: Link's Awakening 30th Anniversary
excerpt_separator: <!--more-->
header:
  teaser: /assets/images/
  og_image: /assets/images/
categories:
  - Blog
tags:
  - gamedev
  - devlog
  - blog/draft
tags:
  - Game development
synced: true
published: false
---
Optional Heart Containers

This hack makes all Heart Containers dropped by bosses optional, by opening the door to the instrument when the boss is defeated rather than when the Heart Container is picked up. This is the way most modern Zelda games do it, including the Link's Awakening remake on Switch.

Link's Awakening forces you to pick up all Heart Containers, which makes for a fairly flat difficulty curve. In addition, Link's Awakening DX is even easier than the original, both in optional ways (the Color Dungeon reward, Medicine in chests, additional Owl Statue hints in dungeons) and in mandatory ways (the Genie boss is nerfed, and the Eagle boss can be cheesed with the Red Tunic). This hack introduces a way to make the game harder as well, to optionally balance it out a little better.

Note that the Heart Container will automatically despawn if you leave the boss room, and will be gone if you return. This is a limitation of the game; in fact, in the un-hacked version, if you defeat a boss and then leave the boss room without picking up the Heart Container (by falling into the pit in the Level 1 boss room, for example), the boss will respawn instead.

Changes:
- Defeating a boss will now open the door to the Siren's Instrument and play the corresponding music (and also still drop a Heart Container)
- Picking up a Siren's Instrument will now refill your hearts (a convenience in case you didn't pick up the Heart Container)




Many Zelda games let you organically set your own difficulty level. Link's Awakening is a fairly short game, and an easy one by most measures, but it's made easier by the fact that there are few ways to set your own difficulty level. You can avoid picking up Heart Pieces and Secret Seashells to not make the game super easy, but that's about it. In Link's Awakening DX, you can make the game even easier with the Color Dungeon. DX also makes the game easier for you, without giving you the option to avoid it, by giving more hints (which cannot be avoided because you can't tell the important hints from the overly helpful ones before reading them), more Medicine, making a boss weaker, etc.





This is an alteration of Link's Awakening DX to commemorate the 30th anniversary of the original Link's Awakening.

A mix of the old and the new, it aims to be a "definitive edition" of Link's Awakening, bringing together features from Link's Awakening (LA), Link's Awakening DX (DX), Link's Awakening for Nintendo Switch (LANS), and a few from the Oracle games (Oracle) - and being compatible with many other hacks - in order to create the ultimate Link's Awakening experience, while still conserving the classic experience.

This hack does not make many major changes, but polishes and balances the game while restoring some original artistic and mechanical choices, following the "redux" tradition.

## Changes

Here's a list of all changes made by this hack.

### Gameplay
- The Power Bracelet is now a passive item instead of an equippable item; simply press A to grab onto something. This reduces the need to change equipment as often. (LANS)
- You can buy back your item from the Goriya while keeping the Boomerang. (LANS)
- Hero Mode: Selectable when starting a new file, Hero Mode has no heart or fairy drops, Link takes twice the damage, and the entire world is mirrored. (LANS)
- Optional Heart Containers: It's now optional to pick up any of the Heart Containers dropped by bosses, which makes it possible to make the game harder at the player's discretion. (LANS)
- Breakable Pots: Pots can now be broken by the L-2 sword and bombs. This also means that bombs cannot be acquired until after Bottle Grotto. (LANS)
- Change Tunic: After getting the red or blue tunic from the Color Dungeon, you can change back to the green tunic at any time from the pause menu. (Start -> Hold Select to bring up submenu -> A)
- Balanced Tunics: The red tunic increases both the damage Link deals and receives, and the blue tunic decreases both the damage Link deals and receives.
- Uncensoring: The censoring related to the Mermaid and the Hippo has been removed. The English dialogue has been inspired by early text from a prototype of the English translation of LA: https://tcrf.net/Development:The_Legend_of_Zelda:_Link%27s_Awakening_(Game_Boy)/Text (LA)
- Skip Dialog: This restores the ability to skip certain text with the B button, which was present in Link's Awakening but removed in DX. In addition, more text in the game has been made skippable, such as the text when you pick up the compass. (LA)
- Improved Maneuverability: Shortcuts have been added; for example, you can now return from the Pothole Maze and Kanalet Castle quickly after solving them. A cave in the Kanalet Castle moat, which was cut from LA, has been reinstated as a shortcut up to Manbo's cave. (LANS)
- Faster Genie: The boss in Bottle Grotto, the Genie, has had his faster speed from Link's Awakening restored, instead of being nerfed like in DX. (LA)
- No Medicine Pick-ups: The Medicine found in dungeon chests in DX have been removed. The Medicine can now only be found at Crazy Tracy's. (LA)
- Fewer Owl Statues: Many of the extra Owl Statues in dungeons, which were added in DX and largely considered overly helpful, have been removed again. (LA)
- Angler's Tunnel
- Dialog speed (Oracle)
- Quick switch items by holding Select and pressing A or B.
- Faster base movement speed after getting the Pegasus Boots.
- You can now steal once from the Tool Shop without being detected, allowing you to get the related photo without dying and being called THIEF. But only once!
- A few secrets and easter eggs have been added.

### Visual/textual
- The title screen is a mix between different designs (LA, LADX, Japanese LADX, LANS), and features the sword animation from ALttP. (LA/DX)
- Sprites no longer partly overlap text boxes, or the status bar at the bottom of the screen, but clip correctly. (Oracle)
- Scrolling Pause Screen: The pause screen now scrolls like in LA, instead of fading in like in DX (which was originally changed to hide a palette change). (LA)
- Desaturate palette: You can change to the [url=https://www.romhacking.net/hacks/7202/]desaturated palette[/url] from the title screen (Select) or pause menu (Start -> Hold Select to bring up submenu -> B), so you can play the same ROM on any device
- Split Key Cavern minimap: The minimap of Key Cavern (Level 3) is properly split into two floors again. (LA)
- Darker Rooms: Dark rooms in dungeons have been made darker than they were in DX. (LA)
- Restored Richard dialogue: Richard's dialogue when you accept his quest was made much less interesting in version 1.2 of Link's Awakening (which carried over to all versions of DX). The original dialogue is restored here. (LA)
- Fixed some typos
- Pressing B when entering name now deletes previous character (Oracle)
- Removed spaces from the end of the player's name. (LANS/Oracle)
- Photo Album Translation: [vince94's Photo Album Translation](https://www.romhacking.net/hacks/2361/)
- The map no longer has a visible grid. (LANS)
- There is a new transition when opening the map.
- Dungeon maps have their levels marked. As a consequence, the subscreen that appears when holding Select has been moved to the left. (LA)
- Some items have been recolored to better match official artwork.
- (Optional) Font Improvement: [Tzepish's non-italic font](https://www.romhacking.net/hacks/927/) for improved readability. (Oracle)


Compatible with:
- Link's Awakening DX v1.0, v1.1 and v1.2
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