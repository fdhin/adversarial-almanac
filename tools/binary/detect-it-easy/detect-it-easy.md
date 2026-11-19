---
name: "detect-it-easy"
categories:
  - blackarch-binary
version: "3.10"
homepage: "https://github.com/horsicq/DIE-engine/releases"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2024-11-24"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "horsicq/DIE-engine"
github-stars: 3344
github-pushed: "2026-11-07"
github-archived: false
github-issues: 19
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1867
gh-activity-checked: "2026-11-09"
---

# detect-it-easy

> Signature-driven identifier for packers, compilers, linkers and protectors across PE, ELF, Mach-O and dozens of other formats.

## What it's for

Detect It Easy (DIE) answers the first question of binary triage — what is
this file and what produced or packed it — using a large, scriptable
signature database that recognizes compilers, packers, protectors,
installers and linkers in PE, ELF, Mach-O and many more formats. It ships
three frontends with the same engine: `die` (full GUI), `diec` (console for
batch work) and `diel` (lightweight GUI), plus `die-update` for the
signature database. Results can be emitted as plain text, JSON, XML, CSV or
TSV, and the scanner can also report file entropy and file info — the
standard first step before deciding on an unpacking or reversing strategy.
It identifies, it does not unpack or emulate.

## Typical use cases

### Identify a binary and its packer

```
diec suspicious.exe
```

Outcome: prints the detected type line(s) — file format plus compiler,
linker, packer or protector signatures that matched (or "unknown" when
nothing hits), giving the starting point for choosing an unpacker or
decompiler setup.

### Check entropy to judge packing

```
diec -e suspicious.exe
```

Outcome: prints entropy information for the file, letting you confirm a
high-entropy packed section (or a normal compiled binary) before investing
time in manual analysis.

### Hunt signatures in overlays and embedded data

```
diec -d -u sample.bin
```

Outcome: runs a deep scan (`-d`) with heuristic methods (`-u`) — slower, but
matches signatures inside resources, overlays and embedded objects that the
fast pass skips.

### Emit machine-readable results

```
diec -j suspicious.exe
```

Outcome: prints the detection results as a JSON object (XML, CSV and TSV via
`-x`, `-c`, `-t`), suitable for feeding a triage pipeline or report.

### Batch-scan a sample directory

```
diec -r -U /malware/samples
```

Outcome: scans the directory recursively (`-r`) and hides entries detected
as unknown (`-U`), printing one detect line per interesting file — a quick
overview of which samples are packed and with what.

### Keep the signature database current

```
diec --checkupdate
diec --update
```

Outcome: `--checkupdate` reports whether a database update is available
(exit code 1 means yes); `--update` downloads and installs it, so newly
added packer and compiler signatures are recognized on the next scan.

*No corpus evidence — derived from upstream documentation.*
