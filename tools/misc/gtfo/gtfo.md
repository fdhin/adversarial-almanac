---
name: "gtfo"
categories:
  - blackarch-misc
version: "17.873d862"
homepage: "https://github.com/mzfr/gtfo"
packaged-by: [blackarch]
blackarch-builddate: "2026-03-09"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "mzfr/gtfo"
github-stars: 461
github-pushed: "2022-07-13"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gtfo

> Search GTFOBins and LOLBAS entries for a binary directly from the terminal.

## What it's for

gtfo is a small Python client for the GTFOBins and LOLBAS datasets: given a binary name it prints the documented abuse techniques (shell escapes, SUID/sudo exploitation, file read/write, and similar) without opening a browser. It is the lookup you reach for mid-engagement — for example right after landing on a host and finding an unexpected SUID binary or a Windows signed executable. It answers "can this binary be abused, and how" for one binary at a time; it is not a host scanner and does not enumerate what is installed on a target.

## Typical use cases

### Look up a Unix binary on GTFOBins

Fetch the documented exploitation techniques for a Linux binary found on a host.

```
gtfo -b vim
```

Outcome: prints the GTFOBins entry for vim with its shell-escape and sudo/SUID abuse techniques and example commands.

### Look up a Windows executable on LOLBAS

Do the same for a signed Windows executable used in living-off-the-land abuse.

```
gtfo -e certutil
```

Outcome: prints the LOLBAS entry for certutil including its documented download/encode abuse patterns.

### List every covered binary

Enumerate the full searchable set before guessing names.

```
gtfo -ls bins
```

Outcome: prints all GTFOBins-covered Unix binaries; `-ls exe` does the same for LOLBAS Windows executables.

*No corpus evidence — derived from upstream documentation.*
