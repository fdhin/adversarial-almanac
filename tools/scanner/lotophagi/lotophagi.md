---
name: "lotophagi"
categories:
  - blackarch-scanner
version: "0.1"
homepage: "http://packetstormsecurity.com/files/55250/lotophagi.rar.html"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/lotophagi"
github-stars: 0
github-pushed: "2019-03-19"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# lotophagi

> Finds default Lotus Notes/Domino databases left exposed on remote hosts.

## What it's for

Lotophagi (Michael Kemp, 2007) is a compact Perl scanner that checks a list of hosts for Lotus Notes/Domino databases still sitting at their default names and reachable over unauthenticated HTTP — `names.nsf`, `catalog.nsf`, `log.nsf`, `statrep.nsf` and dozens more `.nsf`/`.box` paths. A hit typically leaks the Domino directory (users, groups, mail files), server statistics or logs, which is the reconnaissance step for the classic default-database attacks. Interactive prompts, no command-line flags; hosts are read from a file you point it at.

## Typical use cases

### Sweep a host list for default databases

Check many hosts in one pass.

```
lotophagi
```

Outcome: prompts `Check your box? [Y/N]`, then asks for the file containing victim addresses (one host or IP per line); for each host it requests the known `.nsf` and `.box` paths over HTTP and prints every database that responds successfully — anything found can be browsed directly for directory data.
