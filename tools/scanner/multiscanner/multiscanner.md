---
name: "multiscanner"
categories:
  - blackarch-scanner
version: "1559.86e0145"
homepage: "https://github.com/mitre/multiscanner"
packaged-by: [blackarch]
blackarch-builddate: "2019-05-12"
build-system: scripts
license: Mozilla
meta-checked: "2026-11-09"
github-repo: "mitre/multiscanner"
github-stars: 620
github-pushed: "2019-10-08"
github-archived: false
github-issues: 39
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# multiscanner

> Modular file-analysis framework from MITRE: run a configurable suite of analysis modules over a file and aggregate the output.

## What it's for

MultiScanner (MITRE) evaluates files by automatically running a suite of tools and scripts against each sample and merging their output into one report. Analyzers are modules — small Python plugins, web APIs or remote services — enabled and configured per module in `config.ini`, so the framework adapts to whatever analysis pipeline you want; the modules maintained upstream target malware analysis, but nothing restricts it to that. It can be used as a command-line tool, a Python API, or as a distributed stack with a web interface, REST API and queue-backed workers — the CLI covers single-file auditing, the distributed parts are a separate deployment. The packaged build is the 2019 Python 2 era release.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Initialize the working configuration

Generate a default configuration before the first scan.

```
multiscanner init
```

Outcome: writes a default config (config.ini) listing every available module with its options; edit it to enable or disable the analyzers that will run on each file.

### Scan a file

Run the enabled module suite against a sample.

```
multiscanner sample.bin
```

Outcome: every enabled module processes the file and the aggregated report is printed, with results keyed by analyzer — hashes, metadata, detections and whatever the enabled modules produce.

### List the command-line options

See every flag the installed version supports.

```
multiscanner --help
```

Outcome: prints the full CLI option list (configuration path overrides, output handling and related flags) for the installed build.
