---
name: "python-pcodedmp"
categories:
  - blackarch-disassembler
version: "1.2.6"
homepage: "https://github.com/bontchev/pcodedmp"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-13"
build-system: python
license: GPL3
aliases: ["python2-pcodedmp"]
meta-checked: "2026-11-09"
github-repo: "bontchev/pcodedmp"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 490
github-pushed: "2021-06-12"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# python-pcodedmp

> Dumps the compiled VBA p-code of Microsoft Office macro documents — the form that actually executes even when the macro source has been removed.

## What it's for

Macros in Office documents exist in three executable forms: source code, compiled p-code, and "execodes". Most macro analyzers only decompress the stored source, but Office usually ignores that copy and executes the p-code — so a document can have its source removed or replaced with decoy text ("stomped" macros) and still run. pcodedmp (by Vesselin Bontchev) disassembles the p-code of every VBA module in an OLE2 (`.doc`, `.xls`) or OpenXML (`.docm`, `.xlsm`, `.pptm`) document, covering VBA5 (Office 97) through VBA7 (Office 2010+). It only reads documents — it never executes macros — and a few opcodes from 64-bit Office 2016 documents are not disassembled. For going one step further and reconstructing readable VBA source from the p-code, see [[pcode2code]], which builds on this tool's output.

## Typical use cases

### Disassemble the p-code of a document

Show the opcodes that actually execute.

```
pcodedmp -d macro.docm
```

Outcome: prints, per module stream, the disassembled p-code — e.g. `Line #1:` followed by `LitStr 0x001D "This could have been a virus!"` and `ArgsCall MsgBox 0x0003` — revealing behavior even when the stored VBA source is absent or fake. With `-d` only the p-code is shown; drop the flag to also print the parsed `dir` stream records and the identifiers from the `_VBA_PROJECT` stream.

### Triage a whole sample directory

Process every document in a directory tree.

```
pcodedmp /mnt/samples/ -o pcode-report.txt
```

Outcome: recursively walks /mnt/samples/, disassembles every VBA-bearing document found, and writes the combined report to pcode-report.txt instead of stdout. Add `-n` (`--norecurse`) to skip subdirectories.

### Hex-dump the raw streams

Dump underlying bytes for format research.

```
pcodedmp -b macro.doc
```

Outcome: the `dir` and `_VBA_PROJECT` streams, plus the raw bytes of each compiled p-code line, are dumped in hex+ASCII alongside the disassembly — useful when an opcode is unsupported and you need to inspect the bytes yourself.

*No corpus evidence — derived from upstream documentation.*
