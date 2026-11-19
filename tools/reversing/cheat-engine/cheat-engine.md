---
name: "cheat-engine"
categories:
  - reversing
homepage: "https://github.com/cheat-engine/cheat-engine"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: other
license: see repository
meta-checked: "2026-11-13"
github-repo: "cheat-engine/cheat-engine"
github-match: "exact"
github-stars: 19456
gh-checked: "2026-11-13"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# cheat-engine

> Windows memory scanner and debugger built for game modding — find, freeze and script values in any running process; a general-purpose interactive runtime-analysis GUI.

## What it's for

Cheat Engine (Dark Byte) is a Windows development environment focused on modding games and applications: attach to a process, scan its memory for values (exact, unknown, increased/decreased, float, and more), narrow hits down by rescanning, then inspect and edit what lives at an address — freeze a health bar, or script the change. Around that core it packs a debugger with breakpoints and a memory viewer, pointer scanning for stable multi-level paths to a value, a speedhack, Lua scripting (including in-process `{$luacode}` execution), and runtimes for inspecting .NET, Mono (Unity) and Java processes via its data collectors, up to kernel-mode features (DBVM). Built for cheating, it is equally a fast interactive tool for reverse engineering how any application stores and uses data — the same scan-narrow-freeze loop is dynamic analysis. It is a GUI program: there is no user-facing CLI, so workflows are driven through its windows and menus, with Lua as the automation surface. The repository carries no top-level license file; distribution terms live on the project site.

## Typical use cases

### Find where a value lives

The core scan-narrow loop (any process, any game).

```
Open Cheat Engine → click the monitor icon (Open Process) → select target.exe → enter the current value (e.g. 100) → Scan Type: Exact Value → First Scan
```

Outcome: the address list fills with every address currently holding that value; change the value inside the application (spend some currency), enter the new value, and hit Next Scan — repeat until a handful of addresses remain, then double-click one into the table and edit/freeze it to confirm it is the real storage.

### Find the code that changes a value

From an address to the instruction.

```
Right-click an address in the list → Find out what writes to this address
```

Outcome: the debugger attaches and lists every instruction that accesses the address with live registers; Replace/NOP the writer to stop the game from decreasing it — the same view doubles as a general breakpoint workflow on arbitrary memory accesses.

### Make the change survive restarts

Pointer scans instead of raw addresses.

```
Right-click the found address → Pointer scan for this address → accept defaults → after a restart, re-run Pointer scan (rescan memory) on the saved .PTR results
```

Outcome: candidate multi-level pointer paths ending at the target address; filter by rescanning after a restart and keep a stable path, then save the whole session (addresses, pointers, freeze toggles, scripts) with File → Save as a `.ct` cheat table that reloads — and re-applies — in one go.

### Automate and extend with Lua

The scripting surface.

```
Table → Show cheat table Lua script → write `autoAssemble([[...]])` / Lua handlers → Table → Create hotkeys or checkboxes bound to scripts
```

Outcome: the table gains scripted behaviors — auto-assembled code patches, loops, hotkeys, custom dialogs — including `{$luacode}` blocks that execute inside the target process when the Lua client DLLs are built in.

### Inspect managed runtimes

.NET, Unity/Mono and Java targets.

```
Attach to the process → Main menu: Mono → Activate Mono features (Unity) / use the .NET data collector entries for .NET processes
```

Outcome: class and object trees of the managed heap appear alongside raw memory — walk C# object fields by name and edit them symbolically instead of by address, with the monodatacollector/dotnetdatacollector DLLs supplying the runtime introspection.

No corpus evidence — derived from upstream documentation.
