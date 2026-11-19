---
name: "rizin"
categories:
  - reverse-engineering
version: "0.8.2-2"
homepage: "https://rizin.re"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-04-02"
build-system: other
meta-checked: "2026-11-09"
github-repo: "rizinorg/rizin"
github-stars: 4149
github-pushed: "2026-11-05"
github-archived: false
github-issues: 618
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1050
gh-activity-checked: "2026-11-09"
---

# rizin

> Community fork of radare2: a portable reverse-engineering framework and command-line toolset for analyzing, disassembling, patching and debugging binaries.

## What it's for

Rizin is a reverse-engineering framework born as a fork of radare2 (the shared lineage and command grammar make [[radare2]] experience directly portable), reworked with a focus on usability and cleanliness. It opens executables across dozens of architectures (x86, ARM, RISC-V, MIPS, PowerPC, AVR, 8051 and many more) and file formats (ELF, PE, Mach-O, DEX, WASM, UEFI, coredumps), runs analysis passes that recover functions, symbols and variables, and doubles as a scriptable hexadecimal editor and debugger — local sessions or remote GDB/WinDbg servers. Day-to-day work happens in its shell, where single-letter commands (`a` analysis, `p` print, `s` seek) compose; deeper analysis can use partial emulation. The package also ships standalone helpers for shell scripting — `rz-bin` (binary-format information), `rz-asm` (assembler/disassembler), `rz-diff`, `rz-find`, `rz-hash`, `rz-sign`, `rz-ax`, `rz-run` — and the framework is drivable from Python, Ruby, Rust and Go via rzpipe. The official Rizin book documents every command surface.

## Typical use cases

### Analyze a binary and browse its functions

Open a target, run the automated analysis, and inspect what was recovered.

```
rizin -A ./target
afl
pdf @ main
```

Outcome: `-A` maps to the `aaa` analysis pass (`-AA` to `aaaa`), so the session opens with functions and cross-references recovered; `afl` lists every function found and `pdf @ main` prints main's disassembly with argument annotations. For stripped binaries, run the deeper passes manually (`aab`, `aar`, `aac`) after `aa`.

### Hunt for embedded strings

Enumerate the data hiding in a binary's sections.

```
rizin ./target
iz
izz
```

Outcome: opens the file without analysis, `iz` lists the strings in the data sections with their addresses, and `izz` widens the search across the whole file — string offsets double as seek targets (`s <addr>`) for follow-up analysis.

### Debug a program with breakpoints

Drive a native debug session from the same shell.

```
rizin -d ./target
db @ main
dc
dr
```

Outcome: `-d` opens the file in rizin's native debugger without running it; `db @ main` sets a breakpoint at a function name or address, `dc` runs or continues the process, `dr` dumps register state (`drr` telescopes register references, `dbl` lists breakpoints).

### Read the control-flow graph interactively

Walk a function's branching visually.

```
VV
```

Outcome: switches the current function to the interactive graph view; pressing `p` twice more cycles to the minimap mode that packs a larger flow graph on screen — `V` is the family's general visual mode.

*No corpus evidence — derived from upstream documentation.*
