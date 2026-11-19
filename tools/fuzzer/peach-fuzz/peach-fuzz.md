---
name: "peach-fuzz"
categories:
  - blackarch-fuzzer
version: "55.404e8ee"
homepage: "https://github.com/Caleb1994/peach"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Caleb1994/peach"
github-stars: 51
github-pushed: "2016-10-29"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# peach-fuzz

> Pluggable Python scanner that sweeps file trees for dangerous function calls, with an experimental module-driven fuzzing mode.

## What it's for

peach-fuzz packages Caleb1994/peach, a Python vulnerability-scanning framework — unrelated to the Peach Fuzzing Platform despite the name (see [[peach]]). Scans are defined in JSON configuration (default `vulnscan.json`) that maps file criteria (extension, MIME type, executable bit) to plug-and-play modules deciding what counts as unsafe, vulnerable, or dangerous content in each file. The same framework can run as an experimental automated fuzzer via `fuzzing.json`, which executes every file that has the executable bit set — only point it at disposable copies. Findings can be written to JSON. It is Python 2 era code with no declared license.

## Typical use cases

### Scan a source tree with the default rules

```
peach-fuzz -s /path/to/project
```

Outcome: walks the tree applying the `vulnscan.json` rule set and prints each finding — the file plus the dangerous content its scan modules matched.

### Scan with a custom rule set and save JSON results

```
peach-fuzz -s -c myrules.json -o results.json /path/to/project
```

Outcome: runs the modules selected by myrules.json instead of the default configuration and writes all findings to results.json for later triage.

### Follow symlinks but skip hidden files

```
peach-fuzz --follow -nh /path/to/project
```

Outcome: traverses symlinked directories while ignoring hidden files and directories (which are scanned by default), useful on trees heavy with dotfile clutter.

### Experimental fuzzing mode

```
peach-fuzz -f /path/to/binaries
```

Outcome: drives the fuzzers listed in `fuzzing.json`, executing every executable file found with fuzzed input — run it only against throwaway copies in a disposable VM, since it executes everything with the executable bit set.

*No corpus evidence — derived from upstream documentation.*
