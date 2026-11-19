---
name: "cintruder"
categories:
  - blackarch-cracker
version: "14.f8a3f12"
homepage: "https://github.com/epsylon/cintruder"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "epsylon/cintruder"
github-stars: 244
github-pushed: "2020-07-25"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cintruder

> OCR toolkit that harvests captcha samples, learns their glyphs, and cracks similar captchas offline.

## What it's for

CIntruder ("Captcha Intruder") automates captcha bypass for web application testing. The workflow has three phases: harvest captcha samples from a target URL into `inputs/`, "train" the tool on those samples with OCR (splitting images into units and building a word dictionary), then "crack" new captchas by matching them against that dictionary. It ships pluggable OCR modules under `mods/`, a local web GUI for the training step, and post-processing options that export results to XML or inject the recovered word into another tool's command line. It is not a generic OCR library or a commercial captcha-solving API — it targets simple text captchas whose weakness is precisely that they can be re-read offline.

## Typical use cases

### Harvest captcha samples from a target
```
cintruder --track http://target.example.com/captcha --track-num 10
```
Outcome: downloads 10 captcha images from the URL into `./inputs/` and prints a tracking summary with the number of captchas collected; these samples are the raw material for the training step (default count is 5 when `--track-num` is omitted).

### Train OCR on a harvested sample
```
cintruder --train inputs/captcha.png
```
Outcome: applies the OCR algorithm to the image, splits it into units and presents them for recognition; accepted words are added to the local dictionary used later for cracking. Add `--mod <name>` to route through a specific module from `mods/` (list them with `--mods-list`) or `--set-id <0-255>` to key segmentation on a specific colour.

### Crack a captcha against the trained dictionary
```
cintruder --crack inputs/captcha2.png --xml results.xml
```
Outcome: brute-forces the captcha image against the local dictionary, prints the suggested word, and writes the result to `results.xml` in XML format.

### Run the web interface
```
cintruder --gui
```
Outcome: starts the local CIntruder web GUI and opens it in a browser, where tracking and training can be driven interactively with visual feedback on the recognized units.

*No corpus evidence — derived from upstream documentation.*
