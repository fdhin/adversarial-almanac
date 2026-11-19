---
name: "bindead"
categories:
  - blackarch-binary
version: "4504.67019b97b"
homepage: "https://bitbucket.org/mihaila/bindead"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: java
license: GPL3
meta-checked: "2026-11-09"
github-repo: "johannes-riecken/bindead"
github-stars: 0
github-pushed: "2025-05-31"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bindead

> Static analysis of machine code by abstract interpretation over the RREIL intermediate language.

## What it's for

bindead disassembles ELF binaries into RREIL (Relational Reverse Engineering Intermediate Language) and runs interprocedural abstract interpretation over the result, inferring numeric properties such as memory-access bounds without executing the program. It is a university research prototype (primary development on Bitbucket, with a GitHub mirror of the same code), and the author is explicit about its limits: it handles small examples well but can bail out on complex real-world binaries, and its warnings are leads requiring manual inspection rather than confirmed bugs. Reach for it when you want sound reasoning about register and memory values in stripped code — a complement to a disassembler, not a replacement for one. On BlackArch the `bindead` command is a thin wrapper around the Java jar.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Analyze a binary and print warnings

```
bindead --analyze --analysis-summary --show-warnings ./target
```

Outcome: disassembles ./target into RREIL, runs the interprocedural analysis, and prints a summary of the results followed by the warning messages in detail for each potentially unsafe operation the analysis proved possible (for example an out-of-bounds memory access).

### Dump the RREIL translation without analyzing

```
bindead --disassemble --show-rreil-code ./target
```

Outcome: prints only the intermediate representation the analyzer consumes, without running the analysis; add --show-native-code to interleave the original machine instructions for cross-checking.

### Triage an ELF before a long analysis run

```
bindead --file-info --show-sections --show-symbols ./target
```

Outcome: prints the file's metadata, section table, and symbols through the same loader the analyzer uses, confirming the binary parses correctly before committing to the slower full analysis.

### List every analysis option

```
bindead --help
```

Outcome: prints the full usage text, including start-point selection, disassembler frontend choice, and the abstract-domain tuning knobs used by the longer-running analysis modes.
