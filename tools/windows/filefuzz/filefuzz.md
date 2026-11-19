---
name: "filefuzz"
categories:
  - blackarch-windows
version: "1.0"
homepage: "http://www.fuzzing.org/"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: windows
license: GPL2
meta-checked: "2026-11-09"
github-repo: "Seabreg/filefuzz"
github-stars: 0
github-pushed: "2019-03-23"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# filefuzz

> Windows GUI-based binary file fuzzer from the fuzzing.org toolset.

## What it's for

FileFuzz is a graphical Windows file fuzzer distributed via fuzzing.org, the companion site of the book "Fuzzing: Brute Force Vulnerability Discovery". It automates classic mutation-based file fuzzing of file parsers: you select a seed file and a target application, it generates mutated variants of the file, launches the target against each one, and watches for the target to crash so the mutations of interest can be triaged. The attraction is the GUI workflow — iterating on a file format, target application and mutation strategy from one window — rather than throughput or coverage guidance; it is a Windows GUI application with no command-line interface, so it cannot be scripted headlessly. The original fuzzing.org distribution site is long dead; the packaged zip archive is the practical reference copy of the tool.

GitHub: third-party mirror of the dead fuzzing.org distribution (Michael Sutton's original FileFuzz C# source).

*No corpus evidence — derived from upstream documentation.*
