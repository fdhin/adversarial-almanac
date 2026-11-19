---
name: "pipal"
categories:
  - blackarch-cracker
version: "3.4.0.r9.g392f0aa"
homepage: "https://github.com/digininja/pipal"
packaged-by: [blackarch, kali]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "digininja/pipal"
github-stars: 665
github-pushed: "2023-08-27"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pipal

> Robin Wood's password analyser (digininja): feed it a cracked password dump and it produces the statistics a report needs — top passwords, lengths, character mixes, embedded years, and for US lists phone/ZIP-area analysis.

*No corpus evidence — derived from upstream documentation; the usage below was verified by running `pipal.rb --help` at the packaged version 3.4.0 (note the v2-era `--external` flag no longer exists).*

## What it's for

The step after cracking: you have a large list of plaintext passwords — a DC dump from an internal assessment, a breach corpus — and need to say something useful about it. Pipal counts and categorizes: totals and empty passwords, top-N passwords, length distribution, character-set composition, dates and years embedded in passwords, and for US-origin lists phone-number, area-code and ZIP analysis (optionally geolocated with a Google Maps API key via `--gkey`). It changes nothing and cracks nothing; interpretation stays with you — in the author's words, "I give you the numbers, you tell the story." It is self-contained Ruby with a modular checker system that can be extended or toggled, and Ctrl-C mid-run still dumps the statistics gathered so far.

## Typical use cases

### Get a full statistics report for a password dump

```
pipal cracked.txt
```

Outcome: a progress bar while parsing (based on a line count of the file), then the full report on stdout — total analyzed, empty passwords, top passwords, length histogram, character-set breakdowns, embedded numbers and years, plus any enabled checker's findings.

### Widen the top-N lists and write the report to a file

```
pipal -t 20 --output report.txt cracked.txt
```

Outcome: the same analysis with top-20 tables instead of the default top-10, written to report.txt rather than the terminal — the form you paste into a deliverable.

### Produce markdown for a report

```
pipal --markdown cracked.txt
```

Outcome: emits the analysis formatted as markdown (headings and lists) on stdout, ready to redirect into the report document.

### See which analysis modules are active

```
pipal --list-checkers
```

Outcome: lists the available checkers and which are enabled; a module is enabled or disabled by moving its file between the checkers_available/ and checkers_enabled/ directories of the installation.
