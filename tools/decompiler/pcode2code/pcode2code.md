---
name: "pcode2code"
categories:
  - blackarch-decompiler
version: "6.65ae983"
homepage: "https://github.com/Big5-sec/pcode2code"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Big5-sec/pcode2code"
github-stars: 116
github-pushed: "2021-09-15"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pcode2code

> Reconstructs VBA source from the compiled p-code of "stomped" Microsoft Office documents.

## What it's for

Some malicious Office documents are "stomped": the macro source stored in the document is removed or replaced with decoy text while the compiled VBA p-code remains executable. pcode2code decompiles that p-code back into readable VBA, building on pcodedmp's bytecode dump of the OLE2 macro streams. Give it a suspicious `.docm`/`.xls` file (or a pcodedmp dump) and it prints the recovered macro code. It only reads documents — it never executes macros — and it inherits pcodedmp's limitations (a few opcodes and literal forms are unsupported). For a raw bytecode listing use pcodedmp; for deobfuscating the recovered source use a VBA deobfuscator.

## Typical use cases

### Recover VBA source from a stomped document

Decompile the macro p-code of an Office document.

```
pcode2code macro.docm
```

Outcome: prints the decompiled VBA of each macro stream to the console, headed by lines like `stream : VBA/ThisDocument - 2809 bytes`; the recovered code is valid VBA you can read directly or paste into an analyzer.

### Save the recovered code to a file

Write results to a file instead of the terminal.

```
pcode2code macro.docm -o recovered.vba
```

Outcome: the decompiled VBA is written to recovered.vba rather than stdout, ready for diffing or further tooling.

### Decompile an existing pcodedmp dump

Work from a bytecode dump produced earlier.

```
pcode2code -p dump.txt
```

Outcome: treats dump.txt as pcodedmp output rather than an OLE2 document and reconstructs the VBA from the opcode listing — useful when the document itself is no longer at hand.

### Annotate output with p-code line numbers

Correlate recovered source with bytecode lines during triage.

```
pcode2code -n macro.docm
```

Outcome: each output line is annotated with the corresponding p-code line number; note the annotated output is no longer syntactically valid VBA.

### Drive the decompiler from Python

Call the library API from a script.

```
python3 -c "import pcode2code; pcode2code.process('macro.docm', 'recovered.vba')"
```

Outcome: runs the same decompilation in-process via `pcode2code.process(inputfile, outputfile)` and writes recovered.vba — convenient when batching documents in a triage pipeline.

*No corpus evidence — derived from upstream documentation.*
