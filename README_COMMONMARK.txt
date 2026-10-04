# Auto Lua Memory Cleaner

A lightweight, event-driven background memory cleaner designed to help clear out background memory junk during natural breaks.

## Dependencies

Requires **LibAPH** (shared helper library, hard dependency).

Optionals for additional features:
- **LibAddonMenu-2.0:** required for the PC Settings Menu.
- **LibHarvensAddonSettings:** required for the Console Settings Menu.

Without the optional dependencies, the addon still runs entirely independently and can be controlled via built-in slash commands as a standalone utility.

## Why use this over other memory cleaners?

Most memory cleaners run a fixed-interval "OnUpdate" timer that pings your memory every few seconds, looping endlessly from the moment you log in. Some of them do skip the check while you're in combat, but not all of them do - and some still print a memory info line on that same timer regardless of combat state or whether you're even looking at the UI. Most are also built before console APIs existed, and only track "collectgarbage" (ignoring console UI limits).

Auto Lua Memory Cleaner is event-driven first: real triggers (exiting combat, entering a menu, a low-memory warning) do almost all the work. There's still a lightweight ~5-second fallback poll running in the background (one cheap number comparison, not a full scan or UI rebuild) to catch you standing around doing nothing else, but that's a fraction of the constant polling most other memory cleaners run outright.

## Features

- **Near-Zero Idle Footprint:** most checks run only on real triggers - loading screens, exiting combat state, entering a menu - backed by a lightweight ~5-second fallback poll so idle time standing around is still covered without a heavy constant loop.
- **Smart Combat Lockout:** blocks the automatic threshold-based cleanup from running while you're in combat, preventing mid-fight frame drops (imagine crashing in the middle of your Trifecta, or God Slayer run!) - the only exception is for a console low-memory event, where the risk is an outright forced reload. (If you are using "Automatic" as the cleanup mode, memory management is left entirely to the game engine and may not prevent the forced reload - this addon does not touch how the base-game cleanup works.)
- **(PC & Console) Support:** automatically adapts to your hardware specific memory rules. On PC, it helps you stay safely below the 512MB performance "soft limit" to prevent UI lag and stuttering. On Console, it safely monitors the strict 100MB hardware memory pool to prevent the game from forcefully reloading your UI. (If you are using "Automatic" as the cleanup mode, memory management is left entirely to the game engine and may not prevent the forced reload - this addon does not touch how the base-game cleanup works.)
- **Cleanup Method:** pick how ALC clears Lua memory. Background (Recommended, the default) cleans up in small steps over several frames with no stutter. Automatic leaves it to the game engine. Aggressive runs one full pass (a minor stutter) and Deep Clean runs two (a short freeze).
- **Single-Pass Engine Sweep:** a single blocking garbage collection cycle that forces execution of pending `__gc` hooks and clears out orphaned weak tables in one pass, with a smaller chance of catching every ready collectible garbage.
- **Double-Pass Engine Sweep:** a dual-pass garbage collection cycle to safely force execution of all pending `__gc` hooks and ensure orphaned weak tables are properly erased from the addon's Lua heap.
- **Background Sweep:** spreads the same garbage collection work across many game frames instead of running it all at once, so the addon's Lua heap gets cleaned without any single-frame pause large enough to notice (the trade-off is that a full sweep takes a little longer in real time to finish).
- **Module Manager:** soft-disable optional feature files when not needed to save up on CPU usage - re-enable any of them anytime via slash command or the dedicated Module Manager settings.

## Usage & Core Settings

- **Auto-cleanup:** runs silently based on your thresholds.
- **Cleanup threshold:** separate sliders for PC (Lua heap MB) and Console (addon memory pool MB).

## Slash Commands (PC & Console)

- `/alc`: displays commands in chat
- `/alcon`: toggle Auto Lua Cleanup
- `/alcclean`: force manual Lua cleanup
- `/alcpoolreload`: toggle Auto Pool Cleanup After Travel (Console)
- `/alcpoolconfirm`: toggle Auto Pool Cleanup After Travel Confirmation (Console)
- `/alccleanupmode`: switch the Cleanup Method
- `/alcui`: toggle UI
- `/alclock`: lock/unlock UI
- `/alcreset`: reset UI position
- `/alccsa`: toggle Center Screen Announcements
- `/alclogs`: toggle Chat Logs
- `/alcwizard`: re-run Setup Wizard
- `/alclibwarn`: toggle Library Warning Messages
- `/alcbugreport`: open the bug report copy box
- `/alcdelvars`: reset ALL settings to defaults
- `/alcunloadwizard`: toggle unload Wizard module
- `/alcunloadmenu`: toggle unload Menu module
- `/alcunloadmigration`: toggle unload Migration module
- `/alcunloadui`: toggle unload UI module

## System Limits

**Engine Limits & Shared Memory:** because the ESO engine manages memory dynamically in a single global pool, we must rely on smart, threshold-based sweeping rather than passive monitoring. Addons do not run in isolated sandboxes. They share a single global memory pool. It is technically impossible to accurately track memory usage per individual addon without breaking shared libraries and cross-addon communication.

**Important Note On Memory Usage (PC & Console):** unlike PC, where memory scales dynamically with a ~512 MB "soft limit" for UI lag, consoles have a strict 100 MB hardware memory pool for addons. Reaching the console cap will often cause the game to forcefully reload your UI or result in "Out of Memory" crashes.

If an automatic pool reload frees less than 0.5 MB (this can happen after switching between the keyboard and console UI), ALC says so instead of reporting a cleanup: ESO keeps that memory until the game is restarted, so ALC stops reloading for the pool until the next game launch.

While this addon is highly effective at clearing out background "garbage" to keep you under those limits, it cannot magically lower your memory usage if you are running too many heavy addons at once. If your memory remains dangerously high even after a manual cleanup, you should consider disabling a few large addons to ensure stability.

## Does it increase FPS?

No. Nothing here touches rendering, so your frame ceiling is unchanged.

What it protects is the frames you already have. A Lua garbage collection pass costs frame time, and the larger the heap the longer that pause runs. Left alone, the collector picks its own moment, which can be mid-fight. Auto Lua Memory Cleaner collects during dead time instead: a loading screen, the moment you drop out of combat, opening a menu. Keeping the heap small also keeps each pass short.

On Console the same idea covers the 100 MB pool. Clearing it while you are already sitting in a wayshrine loading screen puts the UI reload there, instead of mid-dungeon.

## Does it fix ping or lag spikes?

No. Ping (network latency) and FPS are separate systems - one measures how fast your computer talks to the server, the other measures how fast your CPU/GPU draw frames - and this addon only ever touches the Lua heap and, on console, the addon memory pool. It never reads or changes your connection.

Server-side or network lag shows up as stutter that can feel identical to a frame-rate drop (a burst of delayed data arriving all at once, or characters rubber-banding into position after a connection hiccup), but a Lua memory cleaner has no effect on that kind of stutter, because the cause isn't memory. If your performance dropped along with your connection or the server's condition rather than your addon list, this addon will not be the fix - that's outside what any UI addon can reach.

## Do You Actually Need This? (PC & Console)

**NO.** If your total Lua memory usage consistently stays below 300 MB on PC (with an SSD), or below 70 MB on Console, the native ESO engine is usually efficient enough on its own. This addon is specifically built for:

- **Power Users:** players with dozens of heavy addons pushing memory limits.
- **Console Players:** players already pushing to the 100 MB hardware cap.
- **Performance Freaks / Low-End Users:** anyone wanting manual control over when memory is cleared.

**Console Testing Notes:** This addon was developed and tested on **PC / Steam Deck** (using Force Console Flow for console testing).

## License

Copyright &#169; 2025-2026 @APHONlC. All rights reserved. See LICENSE.md

This add-on is not created by, affiliated with, or sponsored by ZeniMax Media Inc. or its affiliates. The Elder Scrolls&#174; and related logos are registered trademarks or trademarks of ZeniMax Media Inc. in the United States and/or other countries. All rights reserved.

For permissions or inquiries, contact @APHONlC on ESOUI.

## Credits

I would like to thank the following, for providing resources and their awesome projects:

- ESOUI Wiki
- ESO Forums
- @sirinsidiator
- @Flat-Badger-1971
- @sirinsidiator & @Seerah (LibAddonMenu-2.0)
- @Harven & @votan (LibHarvensAddonSettings)
- @SinusPi, @merlight, @Rhyono, @Dolgubon (Zgoo High Isle)
- @Baertram (Mer Torchbug - Fixed and Improved "Variable inspector/Scripts/Events/and more")

Inspired the idea of automatic Lua memory cleanup:
- Shissu's LUA Memory
- Memory Garbage Collector

Testers & Suggestions:
- @phlupp89
- @Drakius192
- @SeablueSky
- @HeyIt'sAmber
- @Lily

Check out my other addons/projects:
- Auto Lua Memory Cleaner
- Permanent Memento
- Tamriel Trade Center, HarvestMap, ESO-Hub, ESOUI Auto-Updater (Linux, macOS, SteamDeck, & Windows)

## Bug Reports

If you encounter any issues, please submit a report on ESOUI
