# NXCLoader

Trainer overlay for Nintendo Switch. It runs under Ultrahand, attaches to the game that is running, and reads a trainer pack (`.nxc`) that matches the game's build ID. The pack describes the menu: toggles, option lists, number entry, hotkeys and actions. Rows change the game's memory and code through Atmosphere's cheat interface.

This repository has no source. Releases hold the overlay.

<img src="images/real-compact-main.jpg" width="480"> <img src="images/real-full-main.jpg" width="480">

South Park: Snow Day!, compact window (left) and full window (right), captured from the console.

## Why I made it

I got tired of the limits Atmosphere's cheat engine puts on a cheat. Real games are not a few values to poke in memory. Most of what I wanted needed code caves, and some needed big ones: telling friend from foe so a damage cheat only hits enemies, scanning memory for a byte pattern at runtime so a cheat survives a game update, calling a function in the game, setting a variable, turning a behaviour on and off while you play. A cheat file can't hold that. The engine caps the code you can have switched on at once at 1,024 dwords (4 KB), one entry at 256 dwords (1 KB), and gives you 16 registers. Go over and it fails without a message.

So NXCLoader runs the code itself. The pack's rows are small programs in a VM that lives in the overlay. They can loop, call each other, follow pointer chains, scan, install a code cave and switch it with a flag. A row can take a number, show a value, offer a list of options, wait on buttons, and put things back when you switch it off.

| | Atmosphere cheat engine | NXCLoader pack |
|---|---|---|
| Code on at once | 1,024 dwords (4 KB) in total | no shared cap, each run has its own budget (up to 1,048,576 instructions) |
| One entry | 256 dwords (1 KB) | one function, up to the same budget |
| Registers | 16 | 64 |
| Entries in a file | 128 | 512 menu nodes, 256 functions, 256 code patches |
| Frozen addresses | 128 | no fixed list, freezes are rows |
| Byte pattern scan | no | yes, up to 64 patterns per pack |
| File size | small text file | up to 8 MB |
| Row controls | on or off | toggle, option list, slider, number entry, action, readouts, hotkeys |
| Switching off | stops the cheat running, what it wrote stays | rows can write the original back |
| Errors | none, the cheat does nothing | the row shows STOP and the Faults list says why |

Memory is still read and written through dmnt, so Atmosphere and the game's files are not modified.

## Install

1. Download the zip from [Releases](https://github.com/bad1dea/NXCLoader/releases/latest) and unzip it onto the SD card. It holds one file, `switch/.overlays/NXCLoader.ovl`.
2. Put the pack for your game at `atmosphere/contents/<title ID>/cheats/<build ID>.nxc`.
3. Start the game, press L3+R3, or open NXCLoader from the Ultrahand list.

Needs Atmosphere with cheats enabled, and Ultrahand.

A pack is read when the overlay attaches. After replacing a pack, restart the game and reopen the overlay.

## Hotkeys

| Buttons | Where | |
|---|---|---|
| L3+R3 | anywhere | Opens NXCLoader. This is the default. Ultrahand stores it per overlay, and you can change it under Open hotkey in the full window. It takes effect at the next start. |
| Up, Down | in the overlay | Move the cursor |
| A | in the overlay | Open a folder, switch a toggle, run an action, step an option list, open number entry |
| Left, Right | in the overlay | Step an option list, slider or number; on other rows, turn the page |
| L, R | compact window | Turn the page |
| B | in the overlay | Up a level; on the first page, close |

Rows can have their own hotkey. Its buttons are drawn as glyphs before the label (Save Coordinates and Teleport to Saved Coordinates in the Snow Day pack do), and the row under it, "change buttons", lets you hold a different combination. Row hotkeys are read while the overlay is open, hidden or pinned.

## Using it

1. Open the overlay with the game running. With a pack loaded it opens the compact window. With no pack it opens the full window, which says why: no game, no pack for this build ID, or the build IDs it does have a pack for.
2. Open a folder with A. Toggles show ON or OFF. Option lists show `< value >` when the cursor is on them.
3. Switch rows on. The game changes straight away.
4. To keep playing with the overlay out of the way, pick Pin over the game. It hands the buttons to the game and leaves a small read-only list on screen: the rows that are on and any readouts the pack has, such as HP or gold. Press the open combo to bring the menu back.
5. Turn all off switches off everything that can reverse itself and tells you how many it switched off and how many cannot be undone.

The first page of the compact window ends with Pin over the game, Turn all off, Full window and Exit. Exit closes the overlay and returns to Ultrahand.

Rows that keep running while you play (held-button rows, readouts) run on the overlay. They keep going while it is open, hidden or pinned and stop when you exit. Rows that patch the game's code keep working after it closes.

If an Atmosphere `.txt` cheat file for the game is on the card or loaded, the overlay shows a warning and lists those cheats in an Atmosphere cheats section with Turn all on and Turn all off. A pack and a `.txt` set for the same game can write the same sites. Whichever writes last wins, and they share the game's small amount of unused code space, so use one or the other.

## Full window

The full window shows the pack's folders, then the overlay's own sections.

<table><tr><td align="center"><img src="images/real-full-main.jpg" width="480"><br>Main</td><td align="center"><img src="images/real-full-player.jpg" width="480"><br>Player</td></tr><tr><td align="center"><img src="images/real-full-team.jpg" width="480"><br>Team</td><td align="center"><img src="images/real-full-enemies.jpg" width="480"><br>Enemies</td></tr></table>

<table><tr><td align="center"><img src="images/real-full-money.jpg" width="480"><br>Money</td><td align="center"><img src="images/real-full-cards.jpg" width="480"><br>Cards</td></tr><tr><td align="center"><img src="images/real-full-world.jpg" width="480"><br>World</td><td align="center"><img src="images/real-full-coordinates.jpg" width="480"><br>Coordinates</td></tr></table>

This session has On now, Turn all off, Faults (when a row has stopped), Save state and Load saved state. Save state records which rows are on and Load saved state switches them on again. Settings has Compact window, Open hotkey, Trainer info and Exit.

<table><tr><td align="center"><img src="images/real-full-session.jpg" width="480"><br>This session</td><td align="center"><img src="images/real-full-settings.jpg" width="480"><br>Settings</td></tr></table>

## Compact window mockups

Every folder of four packs, rendered from each pack's menu data with the Designer's compact view. These are mockups, not photos.

<details open>
<summary>South Park: Snow Day!</summary>

<table>
<tr><td align="center"><img src="images/snowday-f01-player.png" width="190"><br>Player</td><td align="center"><img src="images/snowday-f02-team.png" width="190"><br>Team</td><td align="center"><img src="images/snowday-f03-enemies.png" width="190"><br>Enemies</td><td align="center"><img src="images/snowday-f04-money.png" width="190"><br>Money</td></tr>
<tr><td align="center"><img src="images/snowday-f05-cards.png" width="190"><br>Cards</td><td align="center"><img src="images/snowday-f06-world.png" width="190"><br>World</td><td align="center"><img src="images/snowday-f07-coordinates.png" width="190"><br>Coordinates</td><td align="center"><img src="images/snowday-main.png" width="190"><br>Main</td></tr>
<tr><td align="center"><img src="images/snowday-options.png" width="190"><br>Option list</td></tr>
</table>

</details>

<details>
<summary>Final Fantasy Tactics: The Ivalice Chronicles</summary>

<table>
<tr><td align="center"><img src="images/fft-f01-party.png" width="190"><br>Party</td><td align="center"><img src="images/fft-f02-unit_editor.png" width="190"><br>Unit editor</td><td align="center"><img src="images/fft-f03-speed.png" width="190"><br>Speed</td><td align="center"><img src="images/fft-f04-battle.png" width="190"><br>Battle</td></tr>
<tr><td align="center"><img src="images/fft-f05-hearts_and_crystals.png" width="190"><br>Hearts and crystals</td><td align="center"><img src="images/fft-f06-recruiting.png" width="190"><br>Recruiting</td><td align="center"><img src="images/fft-f07-abilities.png" width="190"><br>Abilities</td><td align="center"><img src="images/fft-f08-gains.png" width="190"><br>Gains</td></tr>
<tr><td align="center"><img src="images/fft-f09-items.png" width="190"><br>Items</td><td align="center"><img src="images/fft-main.png" width="190"><br>Main</td><td align="center"><img src="images/fft-options.png" width="190"><br>Option list</td></tr>
</table>

</details>

<details>
<summary>Super Mario RPG</summary>

<table>
<tr><td align="center"><img src="images/smrpg-f01-party.png" width="190"><br>Party</td><td align="center"><img src="images/smrpg-f02-star_power.png" width="190"><br>Star Power</td><td align="center"><img src="images/smrpg-f03-battle.png" width="190"><br>Battle</td><td align="center"><img src="images/smrpg-f04-auto_action_commands.png" width="190"><br>Auto Action Commands</td></tr>
<tr><td align="center"><img src="images/smrpg-f05-enemies.png" width="190"><br>Enemies</td><td align="center"><img src="images/smrpg-f06-rewards.png" width="190"><br>Rewards</td><td align="center"><img src="images/smrpg-f07-100pct_item_drop_rate.png" width="190"><br>100% Item Drop Rate</td><td align="center"><img src="images/smrpg-f08-shops_and_items.png" width="190"><br>Shops and Items</td></tr>
<tr><td align="center"><img src="images/smrpg-f09-field.png" width="190"><br>Field</td><td align="center"><img src="images/smrpg-f10-game_speed.png" width="190"><br>Game Speed</td><td align="center"><img src="images/smrpg-f11-mini_games.png" width="190"><br>Mini Games</td><td align="center"><img src="images/smrpg-f12-debug.png" width="190"><br>Debug</td></tr>
<tr><td align="center"><img src="images/smrpg-main.png" width="190"><br>Main</td><td align="center"><img src="images/smrpg-options.png" width="190"><br>Option list</td></tr>
</table>

</details>

<details>
<summary>South Park: The Fractured but Whole</summary>

<table>
<tr><td align="center"><img src="images/fractured-f01-combat.png" width="190"><br>Combat</td><td align="center"><img src="images/fractured-f02-movement.png" width="190"><br>Movement</td><td align="center"><img src="images/fractured-f03-money.png" width="190"><br>Money</td><td align="center"><img src="images/fractured-f04-items.png" width="190"><br>Items</td></tr>
<tr><td align="center"><img src="images/fractured-f05-progress.png" width="190"><br>Progress</td><td align="center"><img src="images/fractured-f06-cutscenes.png" width="190"><br>Cutscenes</td><td align="center"><img src="images/fractured-f07-mini_games.png" width="190"><br>Mini Games</td><td align="center"><img src="images/fractured-f08-power_swap.png" width="190"><br>Power Swap</td></tr>
<tr><td align="center"><img src="images/fractured-main.png" width="190"><br>Main</td><td align="center"><img src="images/fractured-options.png" width="190"><br>Option list</td></tr>
</table>

</details>

## How a pack works

A pack is one file keyed to a build ID. The overlay refuses a pack made for a different build.

The menu is a tree of folders and rows. Each row carries a program: read or write an address, compare, loop, call, follow a pointer chain, scan for a byte pattern. The overlay runs it against the game when you change the row. Rows that need new code in the game, such as a hook that scales damage only for enemies, install it into a code cave and switch it with a flag word, so switching the row off stops it without a restart.

## Making packs

Designer is a browser editor for packs. Not released yet, and a guide to writing a pack will come with it. These screenshots show the South Park: Snow Day! project open in it.

The control list on the left, the pack's menu in the middle and the selected control's properties on the right. Each control has a label, a kind (toggle, dropdown, number, slider, readout, button), a folder, and the behavior that runs when it changes.

<img src="images/designer-controls.png" width="800">

A dropdown row, with its option list and what switching it Off does.

<img src="images/designer-dropdown.png" width="800">

Player preview draws the pack as the overlay would, with the same rows and folders.

<img src="images/designer-player-preview.png" width="800">

Behaviors are built from blocks (write, freeze, compare, loop, call) or written as script. The next section shows one in both forms.

<img src="images/designer-behavior-blocks.png" width="800">

Examples walks through common cases, such as freezing a value or hooking an instruction, with the finished behavior for each.

<img src="images/designer-examples.png" width="800">

## What a behavior looks like

A plain row is one line of script. This freezes a value (the address is an example):

```
freeze u32[main+0x4a1b20] = 999
```

Godmode in the Snow Day pack is not a value. It hooks the game's apply-damage function and decides per hit. Here it is in Designer as blocks: each block writes one word of the cave into the game, and the cave's assembly is shown on it.

<img src="images/designer-blocks-godmode.png" width="800">

The same behavior as script. Each line is a write; the last two patch the hook (`main+0x1772550` becomes a branch to the cave) and set the row's flag word.

<img src="images/designer-script-godmode.png" width="800">

The cave itself, as written in the pack's generator. `X9` points at a small table of words in the cave arena. Master code refreshes it every poll with the player's body pointer and team, and each row writes its own flag word there, so switching Godmode off is one store and the hook stays harmless.

```asm
    ; hooked at main+0x1772550, replacing STP X29, X30, [SP, #-0x30]!
        adr  X9, table
        LDR  X10, [X9, #BODY]          ; player's body
        LDR  X11, [X0, #0xF8]          ; body this hit lands on
        LDR  W12, [X9, #god]           ; Godmode flag word
        CBZ  W12, team
        CMP  X10, X11
        B.EQ skip                      ; the player is hit: drop it
    team:
        LDR  W12, [X9, #teamgod]       ; Team Godmode flag word
        CBZ  W12, run
        CBZ  X11, run
        MOV  X16, X9
        MOV  X17, X11
        MOV  X9, X30
        BL   side                      ; friend or foe, below
        MOV  X30, X9
        CMP  W17, #2
        B.EQ skip                      ; on the player's side: drop it
    run:
        STP  X29, X30, [SP, #-0x30]!   ; the instruction the hook replaced
        B    back                      ; continue in the game
    skip:
        RET
```

Friend or foe is a helper that other rows call, so Damage multipliers and AI Speed only touch enemies. It checks the body's vtable, then walks the body's team's enemy list for the player's team class. W17 comes back 0 for not a body, 1 for an enemy, 2 for anyone else on the player's side.

```asm
        LDR  X3, [X17]
        adr  X4, BODY_VTABLE
        CMP  X3, X4
        B.NE none                      ; not a body at all
        LDR  X3, [X17, #PAWN_TEAM]
        LDR  X4, [X16, #TEAM]          ; player's team
        CBZ  X3, ours
        CBZ  X4, ours
        CMP  X3, X4
        B.EQ ours                      ; same team object
        LDR  X4, [X4, #0x10]           ; player's team class
        LDR  X5, [X3, #ENEMY_TEAMS]    ; this team's enemy list
        LDRSW X6, [X3, #ENEMY_TEAMS+8] ; and its count
    scan:
        CBZ  X6, ours
        SUB  X6, X6, #1
        LDR  X12, [X5, X6, LSL #3]
        CMP  X12, X4
        B.NE scan
        MOV  W17, #1                   ; the player's class is in its enemy list
        RET
    ours:
        MOV  W17, #2
        RET
    none:
        MOV  W17, #0
        RET
```

Caves are assembled by the pack's generator and checked by running them in an emulator before they ship, with the flag word on and off, so a cave that changes anything with its word off fails the build.

## Releases

[github.com/bad1dea/NXCLoader/releases](https://github.com/bad1dea/NXCLoader/releases). Current: 0.8.2.
