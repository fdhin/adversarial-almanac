---
name: "stegsolve"
categories:
  - blackarch-stego
version: "1.3"
homepage: "https://github.com/zardus/ctf-tools/blob/master/stegsolve/install"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2020-01-31"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "zardus/ctf-tools"
github-stars: 9569
github-pushed: "2026-10-21"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 59
gh-activity-checked: "2026-11-09"
---

# stegsolve

> Java image analyzer for bit-plane, channel and frame inspection.

## What it's for

Stegsolve 1.3, by ZETA, is a Java GUI for visually dissecting images: it
renders every bit plane of every colour channel (including alpha), so data
hidden in the least significant bits shows up as visible patterns. Its
Analyse menu adds a Data Extractor that carves selected bit planes into a
file, an Image Combiner to merge two images with boolean/arithmetic
operations, a frame browser for animated formats, and a stereogram solver.
It is the standard first-look tool for image stego triage — it finds what
byte-level scanners miss (visual signatures in specific planes) but reports
nothing automatically; the operator interprets what is on screen. The
original distribution site is offline; distributions package the jar with a
launcher. The GitHub link above is the meta-repository whose install script
distributes the jar, not the tool's source.

## Typical use cases

### Launch the image analyzer

Open an image for plane-by-plane inspection.

```
stegsolve image.png
```

Outcome: opens the Stegsolve window with the image loaded; the buttons below the image cycle through colour channels and their bit planes, where LSB-embedded data appears as banding, text or noise patterns that the full-colour view hides.

### Carve specific bit planes to a file

Extract the planes that showed a signal into raw output for further analysis.

```
stegsolve image.png
```

Outcome: via Analyse → Data Extractor, select the bit order, planes and channels (e.g. LSB of red/green/blue); the preview shows the carved result and Save writes it to a file for strings/hex follow-up — the scripted complement to what tools like zsteg do from the command line.

*No corpus evidence — derived from upstream documentation.*
