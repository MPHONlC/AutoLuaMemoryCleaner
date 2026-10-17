[SIZE="5"][COLOR="SeaGreen"]Auto Lua Memory Cleaner[/COLOR][/SIZE]

A lightweight, event-driven background memory cleaner designed to help clear out background memory junk during natural breaks.

[IMG]https://i.postimg.cc/zBvT1dWS/ALC-CLEANING-VID.gif[/IMG]

[SIZE="3"][COLOR="DarkOrchid"]Dependencies:[/COLOR][/SIZE]

This addon requires:
[LIST]
[*] [url="https://www.esoui.com/downloads/info4917-LibAPH.html"][COLOR="#FF69B4"]LibAPH[/COLOR][/url] [COLOR="Gray"][i](Required Unified helpers shared with my addons)[/i][/COLOR]
[/LIST]

Optionals for additional features:
[LIST]
[*] [url="https://www.esoui.com/downloads/info7-LibAddonMenu-2.0.html"][COLOR="#FF69B4"]LibAddonMenu-2.0[/COLOR][/url] [COLOR="Gray"][i](Keyboard/PC Settings Menu)[/i][/COLOR]
[*] [url="https://www.esoui.com/downloads/info584-LibHarvensAddonSettings.html"][COLOR="#FF69B4"]LibHarvensAddonSettings[/COLOR][/url] [COLOR="Gray"][i](Console Settings Menu)[/i][/COLOR]
[/LIST]

[b]Without the optional Dependencies:[/b] You can still run the addon entirely independent, and control its settings via built-in slash commands as a standalone utility.

[SIZE="5"][COLOR="Yellow"]Why use this over other memory cleaners?[/COLOR][/SIZE]

Most memory cleaners run a fixed-interval "OnUpdate" timer that pings your memory every few seconds, looping endlessly from the moment you log in. Some of them do skip the check while you're in combat, but not all of them do - and some still print a memory info line on that same timer regardless of combat state or whether you're even looking at the UI. Most are also built before console APIs existed, and only track "collectgarbage" [COLOR="Gray"][i](ignoring console UI limits)[/i][/COLOR].

Auto Lua Memory Cleaner is event-driven first: exiting combat, entering a menu, a low-memory warning do almost all the work.

[SIZE="5"][COLOR="Yellow"]Features[/COLOR][/SIZE]

[LIST]
[*] [b][COLOR="Lime"]Near-Zero Idle Footprint:[/COLOR][/b] Most checks run only on triggers - loading screens, exiting combat state, entering a menu.
[*] [b][COLOR="Lime"]Smart Combat Lockout:[/COLOR][/b] Blocks the automatic threshold-based cleanup from running while you're in combat, preventing mid-fight frame drops [COLOR="Gray"][i](Imagine crashing in the middle of your Trifecta, or God Slayer run!)[/i][/COLOR] - the only exception is for console low-memory event, where the risk is an outright forced reload. [COLOR="Gray"][i](If you are using "Vanilla" as the cleanup mode leaves memory management entirely to the game engine and may not prevent the forced reload, this addon does not touch how the base-game cleanup work.)[/i][/COLOR]
[*] [b][COLOR="Lime"](PC & Console) Support:[/COLOR][/b] Automatically adapts to your hardware specific memory rules. On PC, it helps you stay safely below the 512MB performance "soft limit" to prevent UI lag and stuttering. On Console, it safely monitors the strict 100MB hardware memory pool to prevent the game from forcefully reloading your UI.
[*] [b][COLOR="Lime"]Console Low-Memory Guard:[/COLOR][/b] When add-on memory reaches 70 MB, where the game warns that it is low, ALC cleans in small steps every frame until it is back under 65 MB, whatever the threshold, minimum growth and delay say, using only a few milliseconds of each frame. It keeps going in combat, and above 80 MB, when the small steps cannot bring memory down, it also runs a Deep Clean, at most every 5 seconds. The game turns add-ons off at about 90 MB. With Reload When Add-On Memory Is Almost Full on [COLOR="Gray"][i](off by default)[/i][/COLOR], ALC asks whether to reload the UI if memory stays above 85 MB for 10 seconds [COLOR="Gray"][i](no answer within 5 seconds means no)[/i][/COLOR], and reloads right away at 88.5 MB, or sooner when memory is climbing fast enough to reach 89.5 MB within a second, after saving every add-on's data. If a reload does not bring it back down, ALC asks you to restart the game to clear that memory back. [COLOR="Gray"][i](make sure to disable the addon holding that much memory and not releasing it to be cleaned as it will happen again, GC even vanilla GC cannot clean memory that is still being held by an add-on without it being released and marked as garbage first.)[/i][/COLOR]
[*] [b][COLOR="Lime"]Reload After Loading Screen When Memory Is Bloated (PC & Console):[/COLOR][/b] Off by default. After a loading screen, if memory is still above the threshold after a full cleanup, ALC saves every add-on's data and reloads the UI, at most once per Minimum Time Between Reloads [COLOR="Gray"][i](60 minutes by default)[/i][/COLOR]. It never reloads in trials, arenas, group dungeons, the Infinite Archive, battlegrounds, Cyrodiil, Imperial City or duels, or while queued for an activity.
[*] [b][COLOR="Lime"]Reload When AFK (PC & Console):[/COLOR][/b] Off by default. After the AFK time [COLOR="Gray"][i](10 minutes by default, 3 to 15)[/i][/COLOR] standing still on the HUD, ALC saves every add-on's data and reloads the UI, then waits until you move before it can do it again. Moving, opening a menu or combat starts the time over. It never reloads in the activities above.
[*] [b][COLOR="Lime"]Garbage Check:[/COLOR][/b] When memory is under the threshold, ALC still runs a Background cleanup once per Lua Cleanup Delay to catch garbage [COLOR="Gray"][i](never in combat or with Vanilla)[/i][/COLOR], once Lua memory has grown by the Min. Growth Since Last Cleanup sliders [COLOR="Gray"][i](2 MB or 5% by default)[/i][/COLOR]. With Garbage Check Ignores Min. Growth on [COLOR="Gray"][i](off by default)[/i][/COLOR] it runs every Lua Cleanup Delay without waiting for growth. It shows in chat and as an announcement only with Garbage Check CSA & Chat Logs on [COLOR="Gray"][i](off by default)[/i][/COLOR].
[*] [b][COLOR="Lime"]Cleanup Method:[/COLOR][/b] pick how ALC clears Lua memory. Automatic [COLOR="Gray"][i](Recommended, the default)[/i][/COLOR] picks the best method each time. Background cleans up in small steps over several frames with no stutter. Vanilla leaves it to the game engine. Aggressive runs one full pass [COLOR="Gray"][i](a minor stutter)[/i][/COLOR] and Deep Clean runs two [COLOR="Gray"][i](a short freeze)[/i][/COLOR].
[*] [b][COLOR="Lime"]Other Add-ons:[/COLOR][/b] Their LibAPH cleanups show in the window and chat too.
[*] [b][COLOR="Lime"]Single-Pass Engine Sweep:[/COLOR][/b] A single blocking garbage collection cycle that forces execution of pending __gc hooks and clears out orphaned weak tables in one pass, with a smaller chance of catching every ready collectible garbage.
[*] [b][COLOR="Lime"]Double-Pass Engine Sweep:[/COLOR][/b] A dual-pass garbage collection cycle to safely force execution of all pending __gc hooks and ensure orphaned weak tables are properly erased from the addon's Lua heap.
[*] [b][COLOR="Lime"]Background Sweep:[/COLOR][/b] Spreads the same garbage collection work across many game frames instead of running it all at once, so the addon's Lua heap gets cleaned without any single-frame pause large enough to notice [COLOR="Gray"][i](the trade-off is that a full sweep takes a little longer in real time to finish)[/i][/COLOR].
[*] [b][COLOR="Lime"]Module Manager:[/COLOR][/b] Soft-disable optional feature files when not needed to save up on CPU usage - re-enable any of them anytime via slash command or the dedicated Module Manager settings.
[/LIST]

[b][COLOR="RoyalBlue"]Usage & Core Settings:[/COLOR][/b]
[LIST]
[*] [b][color=#00FFFF]AUTO-CLEANUP:[/color][/b] Runs silently based on your thresholds.
[*] [b][color=#00FFFF]CLEANUP THRESHOLD:[/color][/b] Separate sliders for PC [COLOR="Gray"][i](Lua heap MB)[/i][/COLOR] and Console [COLOR="Gray"][i](addon memory pool MB)[/i][/COLOR].
[/LIST]

[b][COLOR="RoyalBlue"]Slash Commands [COLOR="Gray"][i](PC & Console)[/i][/COLOR]:[/COLOR][/b]
[LIST]
[*] [b][color=#00FFFF]/alc[/color][/b] - Displays commands in chat
[/LIST]
[spoiler]
[LIST]
[*] [b][color=#00FFFF]/alcon[/color][/b] - Toggle Auto Lua Cleanup
[*] [b][color=#00FFFF]/alcclean[/color][/b] - Force manual Lua cleanup
[*] [b][color=#00FFFF]/alcpoolreload[/color][/b] - Toggle Reload After Loading Screen When Memory Is Bloated
[*] [b][color=#00FFFF]/alcpoolconfirm[/color][/b] - Toggle Ask Before Reloading After a Loading Screen
[*] [b][color=#00FFFF]/alccleanupmode[/color][/b] - Switch the Cleanup Method
[*] [b][color=#00FFFF]/alcui[/color][/b] - Toggle UI
[*] [b][color=#00FFFF]/alclock[/color][/b] - Lock/Unlock UI
[*] [b][color=#00FFFF]/alcreset[/color][/b] - Reset UI Position
[*] [b][color=#00FFFF]/alccsa[/color][/b] - Toggle Center Screen Announcements
[*] [b][color=#00FFFF]/alclogs[/color][/b] - Toggle Chat Logs
[*] [b][color=#00FFFF]/alcwizard[/color][/b] - Re-run Setup Wizard
[*] [b][color=#00FFFF]/alclibwarn[/color][/b] - Toggle Library Warning Messages
[*] [b][color=#00FFFF]/alcbugreport[/color][/b] - Open the bug report copy box
[*] [b][color=#00FFFF]/alcdelvars[/color][/b] - Reset ALL settings to defaults
[*] [b][color=#00FFFF]/alcunloadwizard[/color][/b] - Toggle unload Wizard module
[*] [b][color=#00FFFF]/alcunloadmenu[/color][/b] - Toggle unload Menu module
[*] [b][color=#00FFFF]/alcunloadmigration[/color][/b] - Toggle unload Migration module
[*] [b][color=#00FFFF]/alcunloadui[/color][/b] - Toggle unload UI module
[/LIST]
[/spoiler]

[center]
[SIZE="5"][COLOR="Red"]System Limits[/COLOR][/SIZE]

[b][COLOR="Orange"]Engine Limits & Shared Memory:[/COLOR][/b]
Because the ESO engine manages memory dynamically in a single global pool, we must rely on smart,
threshold-based sweeping rather than passive monitoring. Addons do not run in isolated sandboxes.
They share a single global memory pool. It is technically impossible to accurately track memory usage
per individual addon without breaking shared libraries and cross-addon communication.

[b][COLOR="Orange"]⚠️ Important Note On Memory Usage [COLOR="Gray"][i](PC & Console)[/i][/COLOR]: ⚠️[/COLOR][/b]

If a reload on console frees less than 0.5 MB of the pool [COLOR="Gray"][i](this can happen after switching between the keyboard and console UI)[/i][/COLOR], 
The game keeps that memory until it is restarted, so ALC stops reloading for it until the next game launch.
Unlike PC, where memory scales dynamically with a ~512 MB "soft limit" for UI lag, consoles have
a strict 100 MB hardware memory pool for addons. Reaching the console cap will often cause the
game to forcefully reload your UI or result in "Out of Memory" crashes & forced reload.

While this addon is highly effective at clearing out background "garbage" to keep you under those
limits, [COLOR="Orange"]⚠️ it cannot magically lower your memory usage [/COLOR] if you are running too many heavy addons at
once. If your memory remains dangerously high even after a manual cleanup, you should consider
disabling a few large addons to ensure stability.

[b][COLOR="Yellow"]Does it increase FPS?[/COLOR][/b]
[b][SIZE="4"][COLOR="Red"]NO.[/COLOR][/SIZE][/b] Nothing here touches rendering, so your frame ceiling is unchanged.

What it protects is the frames you already have. A Lua garbage collection pass costs frame time,
and the larger the heap the longer that pause runs. Left alone, the collector picks its own cleanup,
which can be mid-fight. Auto Lua Memory Cleaner collects during dead time instead: a loading screen,
the moment you drop out of combat, opening a menu. Keeping the heap small also keeps each pass short.

On Console the same idea covers the 100 MB pool. Clearing it while you are already sitting in a
wayshrine loading screen puts the UI reload there, instead of mid-combat.

[b][COLOR="Yellow"]Does it fix ping or lag spikes?[/COLOR][/b]
[b][SIZE="4"][COLOR="Red"]NO.[/COLOR][/SIZE][/b] Ping [COLOR="Gray"][i](network latency)[/i][/COLOR] and FPS are separate systems - one measures how fast your computer talks to the server, 
the other measures how fast your CPU/GPU draw frames - and this addon only ever touches the Lua heap and, on console, the addon memory pool. It never reads or changes your connection.

Server-side or network lag shows up as stutter that can feel identical to a frame-rate drop [COLOR="Gray"][i](a burst of delayed data arriving all at once, or characters rubber-banding into position after a connection hiccup)[/i][/COLOR], 
but a Lua memory cleaner has no effect on that kind of stutter, because the cause isn't memory. 
If your performance dropped along with your connection or the server's condition rather than your addon list, this addon will not be the fix - that's outside what any UI addon can reach.

[b][COLOR="Orange"]Do You Actually Need This [COLOR="Gray"][i](PC & Console)[/i][/COLOR]?[/COLOR][/b]
[b][SIZE="4"][COLOR="Red"]NO.[/COLOR][/SIZE][/b] If your total Lua memory usage consistently stays below 300 MB on PC
[COLOR="Gray"][i](with an SSD)[/i][/COLOR], or below 70 MB on Console, the native ESO engine cleanup is
usually efficient enough on its own. This addon is specifically built for:

[b][COLOR="Lime"]Power Users:[/COLOR][/b] Players with dozens of heavy addons pushing memory limits.
[b][COLOR="Lime"]Console Players:[/COLOR][/b] Players already pushing to the 100 MB hardware cap.
[b][COLOR="Lime"]Performance Freaks / Low-End Users:[/COLOR][/b] Anyone wanting manual control over when memory is cleared.

[b][COLOR="Orange"]⚠️ CONSOLE TESTING NOTES ⚠️[/COLOR][/b]
This addon was developed and tested on [b][COLOR="#FF69B4"]PC / Steam Deck[/COLOR][/b] [COLOR="Gray"][i](using Force Console Flow for console testing)[/i][/COLOR].

[SIZE="5"][COLOR="Red"]LICENSE & USAGE[/COLOR][/SIZE]

Copyright © 2025-2026 [COLOR="#FF69B4"]@APHONlC[/COLOR]. All rights reserved. See LICENSE.md

[COLOR="Gray"][i](For permissions or inquiries, contact [COLOR="#FF69B4"]@APHONlC[/COLOR] on ESOUI.)[/i][/COLOR]

[SIZE="5"][COLOR="Red"]Credits[/COLOR][/SIZE]
[b][COLOR="Orange"]I would like to thank the following:[/COLOR][/b]
[COLOR="Gray"][i](For providing resources and their awesome projects)[/i][/COLOR]
[url="https://wiki.esoui.com/Main_Page"][color=#fa9c1b]ESOUI Wiki[/color][/url]
[url="https://forums.elderscrollsonline.com/en/discussion/689370/libharvensaddonsettings-to-libvotan-change-guide"][color=#fa9c1b]ESO Forums[/color][/url]
[url="https://github.com/esoui/esoui"][color=#fa9c1b]@sirinsidiator[/color][/url]
[url="https://www.esoui.com/downloads/info4074-ESOluaAPIintellisenseforVisualStudioCode.html"][color=#fa9c1b]@Flat-Badger-1971[/color][/url]
[url="https://www.esoui.com/downloads/info7.html"][color=#fa9c1b]@sirinsidiator & @Seerah[/color][/url][COLOR="Gray"][i](LibAddonMenu-2.0)[/i][/COLOR]
[url="https://www.esoui.com/downloads/info584.html"][color=#fa9c1b]@Harven & @votan[/color][/url][COLOR="Gray"][i](LibHarvensAddonSettings)[/i][/COLOR]
[url="https://www.esoui.com/downloads/info1624.html"][color=#fa9c1b]@SinusPi, @merlight, @Rhyono, @Dolgubon[/color][/url][COLOR="Gray"][i](Zgoo High Isle)[/i][/COLOR]
[url="https://www.esoui.com/downloads/info2601.html"][color=#fa9c1b]@Baertram[/color][/url][COLOR="Gray"][i](Mer Torchbug - Fixed and Improved "Variable inspector/Scripts/Events/and more")[/i][/COLOR]

[b][COLOR="Orange"]Inspired the idea of automatic Lua memory cleanup:[/COLOR][/b]
[url="https://www.esoui.com/downloads/info883-ShissusLUAMemory.html"][color=#fa9c1b]Shissu's LUA Memory[/color][/url]
[url="https://www.esoui.com/downloads/info4086-MemoryGarbageCollector.html"][color=#fa9c1b]Memory Garbage Collector[/color][/url]

[b][COLOR="Orange"]Testers & Suggestions:[/COLOR][/b]
[color="#FF69B4"]@phlupp89[/color]
[color="#FF69B4"]@Drakius192[/color]
[color="#FF69B4"]@SeablueSky[/color]
[color="#FF69B4"]@HeyIt'sAmber[/color]
[color="#FF69B4"]@Lily[/color]

[b][color=#9CD04C]Check out my other addons/projects:[/color][/b]

[url="https://www.esoui.com/downloads/fileinfo.php?id=4388#info"][color=#fa9c1b]Auto Lua Memory Cleaner[/color][/url]
[url="https://www.esoui.com/downloads/fileinfo.php?id=4116#info"][color=#fa9c1b]Permanent Memento[/color][/url]
[url="https://www.esoui.com/downloads/fileinfo.php?id=3249#info"][color=#fa9c1b]Tamriel Trade Center, HarvestMap, ESO-Hub, ESOUI Auto-Updater[/color][/url] [COLOR="Gray"][i](Linux, macOS, SteamDeck, & Windows)[/i][/COLOR]

[b][color=#ff3300][SIZE="4"]BUG REPORTS[/SIZE][/color][/b]
If you encounter any issues, please submit a report here
[/center]
