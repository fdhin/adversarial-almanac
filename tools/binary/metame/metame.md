---
name: "metame"
categories:
  - blackarch-binary
version: "14.8d583a0"
homepage: "https://github.com/a0rtega/metame"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "a0rtega/metame"
github-stars: 605
github-pushed: "2019-10-06"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# metame

> Rewrite an executable's instructions into logically equivalent variants — a metamorphic engine for x86/x64 binaries.

## What it's for

metame is a metamorphic code engine for arbitrary executables: it opens a
binary, analyzes its code (using radare2 for file parsing and code
analysis), randomly replaces instructions with equivalences in logic and
size, and copies-and-patches the original into a mutated variant. The
output program behaves the same but presents a different byte pattern —
the property metamorphic malware uses to evade pattern-based detection,
which makes metame useful for testing how signature-driven defenses react
to such variants. Supported architectures are x86 32-bit and 64-bit, in
the file formats radare2 understands; radare2 must be installed.

## Typical use cases

### Produce a metamorphic variant of a binary

```
metame -i original.exe -o mutation.exe
```

Outcome: writes `mutation.exe` with randomly substituted,
logic-equivalent instruction encodings; run and diff both binaries to
confirm identical behavior with a changed byte pattern.

### Trace the mutation in detail

```
metame -i original.exe -o mutation.exe -d
```

Outcome: `-d` prints debug information during analysis and patching,
showing which instructions were replaced.

### Maximize substitution

```
metame -i original.exe -o mutation.exe -f
```

Outcome: `-f` forces instruction replacement wherever a substitution is
possible, at the cost of lower metamorphic entropy (per its own help
text) — use when maximal byte-level difference from the original matters.

*No corpus evidence — derived from upstream documentation.*
