---
name: "quickscope"
categories:
  - blackarch-binary
version: "555.180d44f"
homepage: "https://github.com/CYB3RMX/Qu1cksc0pe"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-28"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "CYB3RMX/Qu1cksc0pe"
github-stars: 2101
github-pushed: "2026-09-18"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 32
gh-activity-checked: "2026-11-09"
---

# quickscope

> Triage analyzer for suspicious files: PE, ELF, Mach-O, APK, Office documents, scripts, and archives.

## What it's for

Qu1cksc0pe (packaged as `quickscope`) is a first-pass malware triage tool. It
statically characterizes a sample — Windows, Linux and macOS executables,
Android APKs, Office/VBA and VBScript-family documents, HTML/JS/HTA,
PowerShell, batch and AppleScript files, plus archives — mapping API usage,
strings, and pattern indicators to behavior categories and malware-family
candidates, with bundled YARA rules and hash-database lookups. Document and
script emulators run VBA/JS inside a bounded in-memory sandbox rather than
executing the sample natively. It also offers dynamic observation (`--watch`
on Linux with strace/ltrace and ADB/Frida on Android), an optional AI
summarizer (local Ollama by default), and a web UI. Results are reported as
indicators and candidates, not verdicts, and the tool itself notes when
coverage is partial.

## Typical use cases

### Triage an unknown executable

```
python3 qu1cksc0pe.py --file suspicious.exe --analyze --report
```

Outcome: prints a categorized static analysis (API groups, strings, packer and
family candidates) to the console and writes a structured JSON report under
`sc0pe_reports/` for later diffing or ticketing. On BlackArch the packaged
entry point is simply `quickscope --file suspicious.exe --analyze`.

### Analyze a malicious Office document or VBScript

```
python3 qu1cksc0pe.py --file invoice.xlsm --docs --report
python3 qu1cksc0pe.py --file payload.vbs --docs
```

Outcome: the static document scan runs and the VBA/VBScript project is
additionally emulated in the in-memory sandbox; the report gains an IOC event
trace plus observed network requests, process attempts, persistence activity,
and virtual files the macro touched — without the document's code ever
executing natively.

### Sweep a directory of samples against the hash database

```
python3 qu1cksc0pe.py --folder ./samples --hashscan
```

Outcome: every file in the folder is hashed and matched against the known
malware hash database, flagging previously identified samples so effort can
focus on unknowns.

### Identify packers, signatures, and archive contents

```
python3 qu1cksc0pe.py --file packed.bin --packer
python3 qu1cksc0pe.py --file signed.exe --sigcheck
python3 qu1cksc0pe.py --file dropper.zip --archive
```

Outcome: reports the packer family detected in the binary, checks Authenticode
signature state, and analyzes files inside an archive respectively — quick
answers to "is this packed, signed, or hiding payloads" before deeper
reversing.

### Watch a running Linux process dynamically

```
python3 qu1cksc0pe.py --watch
```

Outcome: starts interactive dynamic monitoring with strace/ltrace covering the
process tree, network connections, opened files, and live-memory YARA/IOC
scanning; observations are emitted as a structured behavior report noting any
collection errors or incomplete coverage.

*No corpus evidence — derived from upstream documentation.*
