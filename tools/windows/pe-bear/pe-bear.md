---
name: "pe-bear"
categories:
  - blackarch-windows
version: "0.7.2"
homepage: "https://github.com/hasherezade/pe-bear"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-06-29"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "hasherezade/pe-bear"
github-stars: 3875
github-pushed: "2026-11-03"
github-archived: false
github-issues: 18
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 73
gh-activity-checked: "2026-11-09"
---

# pe-bear

> Multiplatform Qt GUI for reversing PE files — a fast "first view" for malware analysts that survives malformed binaries.

## What it's for

PE-bear parses and displays Portable Executable files: headers, sections, imports/exports, resources, relocations, Rich header, and Authenticode signature details, with a tree layout designed for a quick first pass over a suspicious sample. Two things distinguish it from other PE viewers: it is deliberately tolerant of malformed and adversarially crafted files (it shows what is there and flags anomalies instead of refusing to parse), and it can load two files side by side to compare them — useful for patched samples or unpacked-versus-original comparisons. It is a GUI application (Qt, builds on Windows/Linux/macOS) with no scripting interface; for automated PE scanning use its command-line sibling pe-sieve or libpeconv-based tooling instead.

## Typical use cases

### Open a suspicious sample for a first look

Start the GUI and load the file you need to triage.

```
wine /usr/share/windows/pe-bear/PE-bear.exe &
```

Outcome: the Qt GUI opens (the BlackArch package ships the upstream Windows build, which runs under Wine; native Linux builds exist from source); File → Open the sample to walk its headers, sections and imports, with anomalies called out rather than hidden.

### Compare two versions of a binary

Load an original and a modified file to see exactly what changed.

```
PE-bear.exe original.exe patched.exe
```

Outcome: PE-bear opens with the two files loaded for side-by-side comparison, highlighting differing fields — the practical way to see what a patch, packer stage or supply-chain modification actually touched.

### Inspect the packaged artifact before use

Check what the package shipped into the vault path.

```
ls /usr/share/windows/pe-bear/ && file /usr/share/windows/pe-bear/PE-bear.exe
```

Outcome: lists the release contents and identifies the main executable as a 64-bit Windows PE — the binary the examples above launch under Wine.

*No corpus evidence — derived from upstream documentation (hasherezade/pe-bear README and wiki).*
