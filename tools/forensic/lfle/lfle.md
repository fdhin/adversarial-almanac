---
name: "lfle"
categories:
  - blackarch-forensic
version: "24.f28592c"
homepage: "https://github.com/williballenthin/LfLe"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "williballenthin/LfLe"
github-stars: 26
github-pushed: "2015-10-09"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# lfle

> Carve classic EVT-format Windows event log records out of a raw disk image.

## What it's for

LfLe (Willi Ballenthin) scans a raw dd-style disk image for byte structures that look like Windows event log records and writes everything that passes its heuristics into a single EVT file. Reach for it when the event log files themselves are deleted, truncated or overwritten — classic Windows 2000/XP/2003 systems where the logs were wiped but record copies remain elsewhere on disk. The output opens in an event log viewer like any EVT file (upstream suggests Event Log Explorer in "direct" mode). It supports only the classic EVT format — not EVTX (Vista and later) — and prints a scan summary to the console.

## Typical use cases

### Carve event log records from an image

Recover log records that no longer sit in a parseable event log file.

```
lfle image.dd recovered.evt
```

Outcome: scans the whole image with percent-complete status, writes recovered records to `recovered.evt`, and prints a summary — records written plus counts skipped for being oversize, undersize, of invalid structure or of invalid content. Open `recovered.evt` in an event log viewer to read them.

### Quiet carve for scripted runs

Drop the progress output when running inside a pipeline.

```
lfle -s image.dd recovered.evt
```

Outcome: the same scan and output file, but percent-complete status messages are suppressed; the final record counts still print (add `-v` for debugging messages during scanning).

*No corpus evidence — derived from upstream documentation.*
