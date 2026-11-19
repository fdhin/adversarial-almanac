---
name: "apkid"
categories:
  - blackarch-mobile
version: "3.0.0"
homepage: "https://github.com/rednaga/APKiD"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-02-01"
build-system: python
license: GPL-3.0-or-later, custom:unknown
meta-checked: "2026-11-09"
github-repo: "rednaga/APKiD"
github-stars: 2613
github-pushed: "2026-10-21"
github-archived: false
github-issues: 83
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 29
gh-activity-checked: "2026-11-09"
---

# apkid

> Identifies the compilers, packers, protectors, obfuscators and other oddities an APK or DEX was built with — PEiD for Android.

## What it's for

APKiD answers "how was this app made?": it runs a large YARA rule set (with yara-python-dex for DEX support) over APKs, DEX files and nested ZIP archives, and reports the compiler (dx, javac...), packers and app-shielding/protector products, obfuscators, anti-analysis tricks and RASP SDK fingerprints it recognizes. It is a natural first step before reverse engineering — knowing the packer or protector decides which unpacking path to take. Inputs are positional file or directory arguments (there is no `-i`/`--input` flag); nested archives are followed to a configurable depth, and the YARA scan time per file is bounded by `-t` (default 30 s).

## Typical use cases

### Fingerprint a single APK

Get the toolchain story of one sample at a glance.

```
apkid target.apk
```

Outcome: prints a version banner followed by one block per scanned entry (e.g. `[*] target.apk!classes.dex`) listing each match as `|-> compiler : dx` style lines — compiler, packer, obfuscator or protector attributed per DEX/APK entry.

### JSON output for scripting

Consume scan results from other tooling.

```
apkid -j target.apk
```

Outcome: same scan, but results are emitted as JSON on stdout instead of the human-readable blocks — feed it to jq or a triage pipeline. Combine with `-t <seconds>` to bound the per-file YARA scan time on heavy samples.

### Write per-file results to a directory

Collect one result file per input for batch triage.

```
apkid -o results/ target.apk second.apk
```

Outcome: writes an individual JSON result file for each scanned input into `results/` (`-o`/`--output-dir` implies JSON output) rather than printing to stdout.

### Inventory a directory of samples

Sweep a whole corpus of APKs/DEX files in one pass.

```
apkid -r samples/
```

Outcome: recurses into `samples/`, scans every APK/DEX it finds (file typing by magic bytes by default; `--typing filename` or `--typing none` changes that), and prints the per-file blocks above — a quick packer/compiler inventory across many samples.

*No corpus evidence — derived from upstream documentation.*
