---
name: "amoco"
categories:
  - blackarch-binary
version: "v2.9.11.r4.g3e36c52"
homepage: "https://github.com/bdcht/amoco"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: python
license: GPL2
meta-checked: "2026-11-09"
github-repo: "bdcht/amoco"
github-stars: 494
github-pushed: "2025-02-17"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# amoco

> Symbolic analysis framework for binaries, with an interactive console and emulator UI.

## What it's for

amoco is a Python package dedicated to symbolic analysis of binaries: compact instruction decoders for many architectures (x86/x64, ARM/Thumb/ARMv8, MIPS, SPARC, RISC-V, AVR, PowerPC, wasm and more), a symbolic algebra for expressing instruction semantics, an abstract memory model that handles concrete and symbolic values transparently, and disassembly strategies from linear sweep and recursive traversal to SAT/SMT-assisted path-predicate recovery. It ships the `amoco` command, a Click frontend that loads a binary and drops you into an IPython console — or an interactive emulator — with the loaded program already in scope. Development is ongoing: some architecture features (x86 FPU semantics, ARM NEON/VFP) are not implemented yet.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Open a binary in the interactive analysis console

Load a program and explore it from Python with amoco already imported.

```
amoco load_program ./target.bin
```

Outcome: the file is parsed by the matching loader and an IPython session starts with the program bound to `p`; command names accept unique prefixes (e.g. `amoco load`) and `-l` forces a specific loader.

### Triage a binary's format and hardening

Print format metadata and checksec-style properties before deeper analysis.

```
amoco bin_info ./target.bin --header
```

Outcome: prints the executable format title, a checksec summary, and — with `--header` — the full ELF/PE/Mach-O header, then falls into the interactive console.

### Emulate a program interactively

Run the target under amoco's emulator with a step/inspect interface.

```
amoco emul_program -i ./target.bin
```

Outcome: starts the console-based emulator UI on the loaded program (step, inspect and save/restore commands, callstack display); `-i` falls back to a plain Python console on exit, `-x` opens the Qt GUI instead.

### Load a program from Python

Use amoco as a library in your own analysis scripts.

```
python3 - <<'PY'
import amoco
p = amoco.load_program("./target.bin")
print(p)
PY
```

Outcome: parses the binary with the appropriate loader and returns a Program object for the loaded file, ready for scripted disassembly or symbolic analysis.
