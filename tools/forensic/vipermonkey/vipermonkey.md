---
name: "vipermonkey"
categories:
  - blackarch-forensic
version: "1160.511ecd5"
homepage: "https://github.com/decalage2/ViperMonkey"
packaged-by: [blackarch]
blackarch-builddate: "2020-10-17"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "decalage2/ViperMonkey"
github-stars: 1132
github-pushed: "2024-07-10"
github-archived: false
github-issues: 74
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---
# vipermonkey

> VBA parsing and emulation engine that runs malicious Office macros in Python to recover IOCs, dropped files and shellcode.

## What it's for

ViperMonkey — created by Philippe Lagadec in 2015-2016, developed mainly by Kirk Sayre since 2017 — parses VBA macros out of Microsoft Office documents (Word, Excel, PowerPoint, Publisher) and then emulates their execution to observe what they would actually do: files written, processes launched, URLs fetched, strings decoded at runtime. Reach for it during maldoc triage when macros are obfuscated and static reading is impractical; it turns the exploitation stage of a weaponized document into concrete indicators, including dropped files (saved to a `<sample>_artifacts/` directory) and injected shellcode bytes. It is explicitly experimental: emulation is slow (run under PyPy and always pass `-s`), it works on some but not all maldocs, and the `--jit` option `eval`s generated Python, so analyze untrusted samples inside a sandbox (the bundled dockermonkey.sh wrapper does that). Despite the name it is unrelated to [[viper]], the binary-analysis and sample-management framework.

## Typical use cases

### Emulate a document's macros

See the behavior of an auto-run macro payload.

```
vmonkey.py -s malicious.docm
```

Outcome: parses and emulates the macros starting from the standard auto-run entry points (AutoOpen, Document_Open, ...) and prints an analysis report of observed behavior; files the macro writes and embedded PE files are extracted to a `malicious.docm_artifacts/` directory. `-s` rewrites VB constructs the parser cannot handle and should almost always be used.

### Report intermediate IOCs

Surface indicators the macro computed but never used.

```
vmonkey.py -s --iocs malicious.docm
```

Outcome: in addition to the behavior report, lists intermediate IOCs tracked during emulation — including injected shellcode byte sequences, which can be saved as a raw file for a shellcode emulator.

### Start emulation at non-standard entry points

Emulate payload functions that the auto-run macros never call directly.

```
vmonkey.py -s -i "Foo,Bar" malicious.docm
```

Outcome: emulation begins at functions Foo and Bar instead of the auto-run entry points (comma-separated list in one quoted argument) — for documents whose real logic sits behind a nonstandard path.

### Tame slow or noisy runs

Finish analysis of samples with heavy loops or excessive logging.

```
vmonkey.py -s -l warning --jit malicious.docm
```

Outcome: lowers the log level to warnings and JIT-converts long-running VB loops to Python, letting runs that previously stalled complete; because `--jit` evaluates generated Python code, keep such runs inside a sandbox.

*No corpus evidence — derived from upstream documentation.*
