---
name: "abcd"
categories:
  - blackarch-disassembler
version: "4.2738809"
homepage: "https://github.com/MITRECND/abcd"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-02"
build-system: scripts
license: BSD-2-Clause
meta-checked: "2026-11-09"
github-repo: "MITRECND/abcd"
github-stars: 16
github-pushed: "2015-10-20"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# abcd

> ActionScript ByteCode (ABC) disassembler for Flash SWF files, from MITRE's incident-response team.

## What it's for

abcd (ABCd) parses the ActionScript 3 bytecode (ABC tags) embedded in SWF files and dumps its structure — scripts, classes, instances, method signatures — followed by the fully disassembled body of every method. It ships two front ends: `abcdump` for text dumps and `abcgraph`, which emits class-relationship graphs as JSON node/edge lists so large sets of SWFs can be clustered and diffed (the classic malicious-Flash triage workflow). It is a Python 2 tool built on pyswf and the upstream has been archived since 2015, so it targets the AVM2 era; it does not decompile bytecode back to ActionScript source.

## Typical use cases

### Dump everything inside a SWF

Print scripts, classes, methods and disassembled bodies of every ABC tag in a file.

```
abcdump sample.swf
```

Outcome: per-script/class/instance/method listings on stdout, then each method body's disassembly as `instruction (opcode): operands` lines with MD5 digests of the raw and operand-stripped code — useful for fingerprinting variants.

### Dump only named scripts

Filter the dump to specific script names.

```
abcdump -s com.example.Main sample.swf
```

Outcome: only the matching script's classes, methods and disassembled bodies are printed; `-s` can be repeated to select several scripts.

### Graph class relationships across SWFs

Build one combined inheritance/instance graph for a corpus of files.

```
abcgraph -f sample.swf more.swf
```

Outcome: writes `nodes.json` and `edges.json` in the working directory (with `-f` including methods and class initializers in the graph) and prints the node/edge counts — import into any graph viewer to see shared class structure between samples.

### Include metadata and embedded binaries in the graph

Pull SWF metadata and binaryData blobs into the same graph.

```
abcgraph -m -b sample.swf
```

Outcome: the JSON output additionally contains nodes for metadata (`-m`) and embedded binary blobs (`-b`), each identified by hash, with the hashes printed as they are added — a quick way to spot shared exploit constants or payloads.

*No corpus evidence — derived from upstream documentation.*
