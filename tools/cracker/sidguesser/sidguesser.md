---
name: "sidguesser"
categories:
  - blackarch-cracker
version: "1.0.5"
homepage: "http://www.cqure.net/wp/tools/database/sidguesser/"
packaged-by: [blackarch, kali]
blackarch-builddate: "2015-09-06"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Seabreg/SIDGuesser"
github-stars: 4
github-pushed: "2019-05-12"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sidguesser

> Enumerates valid Oracle SIDs against a TNS listener using a dictionary.

## What it's for

SIDGuesser by Patrik Karlsson (cqure.net) probes an Oracle database
listener with SID/instance names taken from a dictionary file. A TNS
listener responds differently to SIDs that exist, so the tool separates
valid instance names from invalid ones — usually the first step before
credential guessing against a specific database. It is single-purpose: it
finds SID names, not accounts or passwords. Licensed under the GPL
(version 2 or, at your option, any later version).

## Typical use cases

### Guess SIDs against a default listener

Give the listener address and a SID dictionary; the default port is 1521.

```
sidguesser -i 198.51.100.10 -d oracle-sids.txt
```

Outcome: tests every dictionary entry against the listener (default mode
`findall`) and reports which SID names it accepts, with a live statistics
display while running.

### Probe a nonstandard port and log to a file

Use `-p` for the port, `-r` to write a report, and `-m findfirst` to stop
at the first valid SID.

```
sidguesser -i 198.51.100.10 -p 1522 -d oracle-sids.txt -r results.txt -m findfirst
```

Outcome: halts as soon as one valid SID is found and writes the findings
to `results.txt`, e.g. when only a single instance name is needed for the
next step.

*No corpus evidence — derived from upstream documentation.*
