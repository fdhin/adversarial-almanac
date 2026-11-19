---
name: "flasm"
categories:
  - blackarch-decompiler
version: "1.62"
homepage: "http://www.nowrap.de/flasm.html"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2025-12-14"
build-system: make
license: custom
meta-checked: "2026-11-09"
worth: 1.1
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# flasm

> Flash ActionScript bytecode disassembler and assembler — dump a SWF's bytecode, edit it, and write it back.

## What it's for

Flasm disassembles the ActionScript bytecode of SWF files (Flash 8 and earlier)
into a textual, editable project format and assembles the edited project back
into the original SWF, keeping a backup of the untouched file. It disassembles
the entire SWF — all timelines, frames and event handlers — which makes it
useful both for learning how the Flash compiler emits code and for patching a
compiled SWF when the ActionScript source is unavailable. It also compresses
and decompresses the SWF container with zlib. It is not a decompiler: it shows
bytecode mnemonics, not reconstructed ActionScript source.

## Typical use cases

### Disassemble a SWF to an editable project file

Dump the full bytecode of a movie so it can be read or edited as text.

```
flasm -d movie.swf > movie.flm
```

Outcome: the complete disassembly of every timeline and event handler in
`movie.swf` is written to `movie.flm` (running `flasm movie.swf` with no
command has the same effect).

### Assemble an edited project back into its SWF

Apply the changes made in a `.flm` disassembly back into the movie.

```
flasm -a movie.flm
```

Outcome: the SWF named inside `movie.flm` is rebuilt in place and the original
is preserved as a `.$wf` backup next to it.

### Optimize and update an SWF in one step

Re-emit a final SWF with Flasm macros expanded and trivial optimizations applied.

```
flasm -u movie.swf
```

Outcome: Flasm disassembles to a temporary file, executes embedded macros,
applies trivial optimizations (rebuilding constant pools, removing double
nots), backs up the original as `.$wf` and rewrites `movie.swf` slightly
smaller.

### Decompress a compressed SWF for other tools

Turn a zlib-compressed SWF into an uncompressed one that any tool can parse.

```
flasm -x movie.swf
```

Outcome: `movie.swf` is written decompressed with the original kept as `.$wf`;
`flasm -z movie.swf` compresses it back (only Flash MX+ players read
compressed files).

*No corpus evidence — derived from upstream documentation (author docs at
nowrap.de).*
