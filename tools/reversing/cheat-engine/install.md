---
tool: "cheat-engine"
build-system: "other"
upstream: "https://github.com/cheat-engine/cheat-engine"
recipe: "upstream"
license: "see repository"
---

# Installing cheat-engine

> Windows memory scanner/debugger GUI for game modding and interactive runtime analysis.

Source: hacktricks feed; upstream [`cheat-engine/cheat-engine`](https://github.com/cheat-engine/cheat-engine).

## Download the installer (recommended)

Grab the latest release installer from the [Releases](https://github.com/cheat-engine/cheat-engine/releases/latest) page (older versions also available). The project site and wiki are at [cheatengine.org](https://www.cheatengine.org) / [wiki.cheatengine.org](https://wiki.cheatengine.org).

Notes:

- Runs on Windows; many operations want an elevated (administrator) process, and kernel-mode features additionally require the DBK driver (built unsigned — boot with test signing or sign it yourself).
- Antivirus products routinely flag the installer/driver as a hacktool; expect that on analysis boxes.

## Build from source

1. Install Lazarus 2.2.2 with FPC 3.2.2 (win64, then the cross-i386 addon).
2. Open `cheatengine.lpi` from the `Cheat Engine` folder in Lazarus and Build (SHIFT+F9).

Secondary projects add capabilities and are compiled separately (32- and 64-bit where noted): `speedhack.lpr` (speed control DLLs), `luaclient.lpr` (`{$luacode}`), `monodatacollector.sln` (Mono/.NET inspection), `dotnetdatacollector.sln` (.NET symbols), `dotnetinvasivedatacollector.sln` (runtime JIT support), `cejvmti.sln` (Java inspection), `vehdebug.lpr` (VEH debugger), `DirectXMess.sln` (D3D overlay), `DotNetcompiler.sln`, `tcclib.sln` (inline C), `dbkkernel.sln` (kernel mode, unsigned). `.sln` files need Visual Studio (usually 2017).

Backlinks: [[reversing]] · [[cheat-engine]]
