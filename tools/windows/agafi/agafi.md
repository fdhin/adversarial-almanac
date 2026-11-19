---
name: "agafi"
categories:
  - blackarch-windows
version: "1.1"
homepage: "https://github.com/helpsystems/Agafi"
packaged-by: [blackarch]
blackarch-builddate: "2019-01-24"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "helpsystems/Agafi"
github-stars: 97
github-pushed: "2021-06-13"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# agafi

> Core Security's Win32 gadget finder and ROP-chain builder — find x86 gadgets matching register-level objectives, then assemble DEP-bypassing chains.

## What it's for

Agafi ("Advanced Gadget Finder", from Core Security's exploit-writing team) is a pair of Windows console tools for x86 exploit development. `agafi.exe` searches for ROP gadgets — in an on-disk module, a running process, or a previously captured memory snapshot — that match an objective written in a small constraint language such as `[esp+0x00]==esi and [esp+0x04]==ebp and eip==edi`, with optional x86 emulation and black-box analysis of what a candidate gadget actually does. `agafi-rop.exe` then chains found gadgets with the PUSHAD/RET technique to build ROP chains that call `kernel32.VirtualProtect` and bypass DEP. It targets the Win32 era (Windows XP SP2 and later) and needs the distorm3 disassembler library at runtime. BlackArch installs the binaries, both user guides, and the gisnap/fsnap snapshot helpers (including OllyDbg and WinDbg integrations) under /usr/share/windows/agafi/, where the executables run under Wine.

## Typical use cases

### Find gadgets in a binary

Search an executable on disk for instruction sequences matching an objective file.

```
agafi.exe -f target.exe objective.txt
```

Outcome: every gadget in target.exe matching the objectives is printed with its address, its instructions (for example `pusha` / `ret`), the matched conditions and the stack delta; pass an output file as a third argument to write the results to disk instead. objective.txt can also pin modules (`-modules=...`), restrict the searched address range (`-test_range=BASE,LIMIT`) and assign register or EFLAGS values.

### Search a running process or a saved snapshot

Take gadgets from live memory or a memory image instead of a file on disk.

```
agafi.exe -p 1234 objective.txt
agafi.exe -s snapshot.bin objective.txt
```

Outcome: the first form analyzes the process with PID 1234, the second a previously captured snapshot file; prefixing the option with `r` (`-rp 1234`, `-rs snapshot.bin`) reuses an already-taken snapshot instead of capturing a new one.

### Build a DEP-bypass ROP chain

Let the chainer assemble a VirtualProtect chain for a vulnerable module.

```
agafi-rop.exe -f target.dll settings.txt
```

Outcome: agafi-rop runs its gadget search over target.dll and prints the reconstructed PUSHAD/RET chain (image base, sections and gadgets) that calls kernel32.VirtualProtect; settings.txt constrains the build — for example the characters that must not appear in the chain.

*No corpus evidence — derived from upstream documentation.*
