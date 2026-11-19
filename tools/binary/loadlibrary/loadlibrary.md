---
name: "loadlibrary"
categories:
  - blackarch-binary
version: "104.c40033b"
homepage: "https://github.com/taviso/loadlibrary"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2022-01-30"
build-system: make
license: GPL2
meta-checked: "2026-11-09"
github-repo: "taviso/loadlibrary"
github-stars: 4509
github-pushed: "2025-04-10"
github-archived: false
github-issues: 45
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# loadlibrary

> Load Windows DLLs natively on Linux — a dlopen()-like PE/COFF loader for fuzzing and analyzing Windows library code.

## What it's for

loadlibrary (Tavis Ormandy) is a userspace PE/COFF loader derived from
ndiswrapper: it processes relocations and imports and exposes a
`dlopen`-like API so native Linux programs can load a Windows DLL and call
its exported functions directly. The flagship demonstration ports
Microsoft Defender's mpengine.dll into a Linux test client, enabling
scalable fuzzing of the antivirus engine with ASAN and Valgrind, gdb
debugging with symbols, basic-block coverage collection, and runtime
hooking and patching — all without a Windows VM. Good targets are
self-contained libraries: codecs, decompressors, image decoders, virus
engines; 32-bit DLLs need 32-bit build dependencies. It is not Wine or
Winelib: it does not run Windows applications, it lets your own Linux
harness code call into a DLL.

## Typical use cases

### Scan a file with the ported Windows Defender engine

```
./mpclient eicar.com
```

Outcome: the test client loads `engine/mpengine.dll` (the engine and
definitions directory), runs the scan, and prints
`EngineScanCallback(): Threat Virus:DOS/EICAR_Test_File identified.` —
the Windows engine running natively on Linux.

### Debug the loaded DLL under gdb with symbols

```
gdb -q ./mpclient
```

Outcome: mpclient detects the debugger, traps, and prints the exact
`add-symbol-file` and `genmapsym.sh` commands (generated from an IDA map
file) to paste into gdb; afterwards breakpoints, watchpoints, and
backtraces work on DLL-internal functions.

*No corpus evidence — derived from upstream documentation.*
