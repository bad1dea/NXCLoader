# NXCLoader

A trainer overlay for the Switch. It runs under Ultrahand, hooks into whatever game you have running, and loads a trainer pack (`.nxc`) made for that exact build of the game. The pack is the menu: toggles, dropdowns, number entry, hotkeys, buttons. Flip a row and it changes the game.

There's no source in this repo. Releases have the overlay.

<img src="images/real-compact-main.jpg" width="480"> <img src="images/real-full-main.jpg" width="480">

That's South Park: Snow Day!, compact window on the left and the full window on the right, straight off the console.

## Why I made it

I got sick of the limits on Atmosphere's cheat engine. Games aren't a handful of values you poke in memory. Most of what I wanted needed code caves, and a lot of them needed big ones: telling friend from foe so a damage cheat only hits enemies, scanning memory for a byte pattern so a cheat survives a game update, calling a function in the game, setting a variable, turning something on and off while you play.

A cheat file can't hold any of that. The engine gives you 1,024 dwords (4 KB) of code for everything you have switched on, 256 dwords (1 KB) per entry, and 16 registers. Go over and it just fails. No error, the cheat does nothing.

So NXCLoader runs the code itself. Each row in a pack is a little program in a VM that lives in the overlay. It can loop, call other functions, follow pointer chains, scan memory, and drop in a code cave and switch it with a flag. A row can take a number, show you a value, give you a list of options, wait on buttons, and put things back when you turn it off.

| | Atmosphere cheat engine | NXCLoader pack |
|---|---|---|
| Code on at once | 1,024 dwords (4 KB) total | no shared cap, each run gets its own budget (up to 1,048,576 instructions) |
| One entry | 256 dwords (1 KB) | one function, same budget |
| Registers | 16 | 64 |
| Size of a set | 128 entries | 512 menu rows, 256 functions, 256 code patches |
| Frozen addresses | 128 | no list, a freeze is just a row |
| Byte pattern scan | no | yes, up to 64 patterns per pack |
| File size | small text file | up to 8 MB |
| Row controls | on or off | toggle, dropdown, slider, number entry, button, readouts, hotkeys |
| Turning off | stops the cheat, whatever it wrote stays in the game | a row can write the original back |
| Errors | none | the row shows STOP and Faults tells you why |

It still reads and writes the game through dmnt, so nothing on your Atmosphere or in the game's files gets touched.

## Install

1. Grab the zip from [Releases](https://github.com/bad1dea/NXCLoader/releases/latest) and unzip it onto your SD card. It's one file: `switch/.overlays/NXCLoader.ovl`.
2. Put the pack for your game at `atmosphere/contents/<title ID>/cheats/<build ID>.nxc`.
3. Start the game, press L3+R3 (or pick NXCLoader from the Ultrahand list).

You need Atmosphere with cheats on, and Ultrahand.

The overlay reads the pack when it hooks in, so if you swap a pack, restart the game and open the overlay again.

## Buttons

| Buttons | | |
|---|---|---|
| L3+R3 | anywhere | Opens NXCLoader. That's the default. It's Ultrahand's per-overlay combo, so change it under Open hotkey in the full window (takes effect next time you start it) |
| Up, Down | in the overlay | Move the cursor |
| A | in the overlay | Open a folder, flip a toggle, run a button, step a dropdown, open number entry |
| Left, Right | in the overlay | Step a dropdown, slider or number. On any other row they flip the page |
| L, R | compact window | Flip the page |
| B | in the overlay | Back a level. On the first page it closes the overlay |

Some rows have their own hotkey. You'll see the button glyphs in front of the name (Save Coordinates and Teleport to Saved Coordinates in the Snow Day pack have them). The "change buttons" row under it lets you hold down a different combo. Row hotkeys work while the overlay is open, hidden or pinned.

## Using it

1. Open the overlay with the game running. With a pack loaded you get the compact window. With no pack you get the full window, which tells you why: no game, no pack for this build ID, or which build IDs it does have packs for.
2. Open a folder with A. Toggles say ON or OFF, and dropdowns show `< value >` when the cursor is on them.
3. Flip rows on. The game changes right away.
4. Want to play with it out of the way? Pick "Pin over the game". It hands the buttons back to the game and leaves a small list on screen with what's on plus any readouts the pack has (HP, gold). Press the open combo to get the menu back.
5. "Turn all off" switches off everything that can undo itself, and tells you how many it turned off and how many can't be undone.

The first page of the compact window ends with Pin over the game, Turn all off, Full window and Exit. Exit closes the overlay and sends you back to Ultrahand.

Rows that keep running while you play (held-button rows, readouts) run from the overlay. They keep going while it's open, hidden or pinned, and stop when you exit. Rows that patch the game's code keep working after you close it.

If there's an Atmosphere `.txt` cheat file for the game on the card or loaded, the overlay warns you and lists those cheats under Atmosphere cheats, with Turn all on and Turn all off. A pack and a `.txt` set for the same game can write the same spots, and whoever writes last wins. They also fight over the little bit of free code space in the game, so use one or the other.

## Full window

The full window has the pack's folders first, then the overlay's own sections.

<table><tr><td align="center"><img src="images/real-full-main.jpg" width="480"><br>Main</td><td align="center"><img src="images/real-full-player.jpg" width="480"><br>Player</td></tr><tr><td align="center"><img src="images/real-full-team.jpg" width="480"><br>Team</td><td align="center"><img src="images/real-full-enemies.jpg" width="480"><br>Enemies</td></tr></table>

<table><tr><td align="center"><img src="images/real-full-money.jpg" width="480"><br>Money</td><td align="center"><img src="images/real-full-cards.jpg" width="480"><br>Cards</td></tr><tr><td align="center"><img src="images/real-full-world.jpg" width="480"><br>World</td><td align="center"><img src="images/real-full-coordinates.jpg" width="480"><br>Coordinates</td></tr></table>

This session has On now, Turn all off, Faults (shows up when a row has stopped), Save state and Load saved state. Save state remembers which rows are on, and Load saved state turns them back on. Settings has Compact window, Open hotkey, Trainer info and Exit.

<table><tr><td align="center"><img src="images/real-full-session.jpg" width="480"><br>This session</td><td align="center"><img src="images/real-full-settings.jpg" width="480"><br>Settings</td></tr></table>

## Compact window mockups

Every folder from four packs, drawn from each pack's menu data with the Designer's compact view. These are mockups, not photos.

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

A pack is one file for one build of a game. If the build doesn't match, the overlay won't load it.

The menu is a tree of folders and rows, and each row carries a program: read or write an address, compare, loop, call, follow a pointer chain, scan for a byte pattern. The overlay runs it against the game when you change the row. Rows that need new code in the game, like a hook that scales damage for enemies only, put it in a code cave and switch it with a flag word. Turning the row off just clears the flag, so there's no restart.

## Making packs

Designer is a browser editor for packs, and a guide to writing them comes with it. It's not out yet. Here's the Snow Day project open in it.

You can also drop in your Atmosphere `.txt` cheats and Designer converts them into a pack: the cheat codes become rows, and sets of cheats that only differ by a number (2x, 5x, 10x) fold into one dropdown.

Controls on the left, the pack's menu in the middle, and the selected control's settings on the right. Every control has a label, a kind (toggle, dropdown, number, slider, readout, button), a folder, and the behavior that runs when it changes.

<img src="images/designer-controls.png" width="800">

A dropdown row, with its options and what Off does.

<img src="images/designer-dropdown.png" width="800">

Player preview draws the pack the way the overlay will, same rows and folders.

<img src="images/designer-player-preview.png" width="800">

Behaviors are built from blocks (write, freeze, compare, loop, call) or you can type them as script.

<img src="images/designer-behavior-blocks.png" width="800">

Examples walks you through the usual cases, like freezing a value or hooking an instruction, with the finished behavior for each.

<img src="images/designer-examples.png" width="800">

## What a behavior looks like

Simple ones are a single line. This freezes a value (the address is made up):

```
freeze u32[main+0x4a1b20] = 999
```

Godmode in the Snow Day pack is a real code cave. It hooks the game's apply-damage function and decides on every hit. This is how Designer shows it: the whole cave as one listing, with the hook it's attached to on top.

<img src="images/designer-blocks-godmode.png" width="800">

And this is the cave as I wrote it. `X9` points at a small table of words that master code refills every poll with the player's body and team. Each row sets its own flag word in that table, so turning Godmode off is one store and the hook stays harmless.

```asm
    ; hooked at main+0x1772550, replacing STP X29, X30, [SP, #-0x30]!
        adr  X9, table
        LDR  X10, [X9, #BODY]          ; the player's body
        LDR  X11, [X0, #0xF8]          ; the body this hit lands on
        LDR  W12, [X9, #god]           ; Godmode flag
        CBZ  W12, team
        CMP  X10, X11
        B.EQ skip                      ; it's the player: throw the hit away
    team:
        LDR  W12, [X9, #teamgod]       ; Team Godmode flag
        CBZ  W12, run
        CBZ  X11, run
        MOV  X16, X9
        MOV  X17, X11
        MOV  X9, X30
        BL   side                      ; friend or foe, below
        MOV  X30, X9
        CMP  W17, #2
        B.EQ skip                      ; on the player's side: throw it away
    run:
        STP  X29, X30, [SP, #-0x30]!   ; the instruction the hook replaced
        B    back                      ; carry on in the game
    skip:
        RET
```

Friend or foe is its own helper so other rows can use it too. Damage multipliers and AI Speed only touch enemies because of it. It checks the thing is a body, then looks through that body's team for the player's team in its enemy list. W17 comes back 0 for "not a body", 1 for an enemy, 2 for anyone on the player's side.

```asm
        LDR  X3, [X17]
        adr  X4, BODY_VTABLE
        CMP  X3, X4
        B.NE none                      ; not a body at all
        LDR  X3, [X17, #PAWN_TEAM]
        LDR  X4, [X16, #TEAM]          ; the player's team
        CBZ  X3, ours
        CBZ  X4, ours
        CMP  X3, X4
        B.EQ ours                      ; same team
        LDR  X4, [X4, #0x10]           ; the player's team class
        LDR  X5, [X3, #ENEMY_TEAMS]    ; this team's enemy list
        LDRSW X6, [X3, #ENEMY_TEAMS+8] ; and how many are in it
    scan:
        CBZ  X6, ours
        SUB  X6, X6, #1
        LDR  X12, [X5, X6, LSL #3]
        CMP  X12, X4
        B.NE scan
        MOV  W17, #1                   ; player's class is in its enemy list: foe
        RET
    ours:
        MOV  W17, #2
        RET
    none:
        MOV  W17, #0
        RET
```

Every cave gets run in an emulator before it ships, with its flag on and off, so one that changes something while its flag is off gets caught.

## Releases

[github.com/bad1dea/NXCLoader/releases](https://github.com/bad1dea/NXCLoader/releases). Current: 0.8.2.
