---
name: "42zip"
categories:
  - blackarch-dos
version: "42"
homepage: "http://blog.fefe.de/?ts=b6cea88d"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "scalegency/42zip"
github-stars: 0
github-pushed: "2026-08-23"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# 42zip

> The reference recursive zip bomb — a 42 KB archive that expands to roughly 4.3 PB of data when extracted layer by layer.

*No corpus evidence — this package ships the well-known 42.zip artifact itself, not a program.*

## What it's for

Testing how software behaves when asked to decompress something far larger than it expects. The package installs the classic 42.zip file (under `/usr/share/42zip/42.zip` on BlackArch): a small archive whose entries are further zip archives, nested several levels, ending in large highly-compressible payloads. A tool that recursively extracts with no depth or size limits will exhaust disk or memory; a well-behaved archiver stops, warns, or expands only one layer. Use it to verify defenses in antivirus engines, mail gateways, file-analyzers and CI pipelines before an attacker supplies one. It is a test payload, not a delivery mechanism — never send it to systems you do not own.

## Typical use cases

### Check what a single-layer listing reveals

```
7z l -slt /usr/share/42zip/42.zip
```

Outcome: the top level only — a handful of nested archive entries with tiny compressed sizes. The metadata view is the safe way to demonstrate the bomb's structure without triggering expansion, and it is exactly the triage step that protected tooling should be doing automatically.

### Exercise an extraction pipeline's depth limit

```
7z x /usr/share/42zip/42.zip -o/tmp/bombtest
```

Outcome: the first layer extracts to a set of nested zips in `/tmp/bombtest` and stops there — `7z` does not recurse automatically. The interesting behavior is what your target tool does next: a scanner or pipeline that recursively unpacks those inner archives with no budget will keep expanding toward the petabyte-scale total until it hits its limits; that failure mode is the finding.
