---
name: "depant"
categories:
  - blackarch-cracker
version: "0.3a"
homepage: "http://midnightresearch.com/projects/depant/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# depant

> Two-phase default-password scanner: nmap maps the network's services, hydra tests them against default credentials, then the fastest service re-runs a larger list.

## What it's for

depant (DEfault Password audit Network Tool) automates the classic "what on this network still runs factory credentials" check. Phase one nmap-scans the target range, identifies open services, and has hydra (both are hard dependencies) try each service against a default username/password list — downloadable from the Phenoelit collection with `-g`. Phase two is the clever part: it re-runs a larger, user-supplied credential set against only the fastest-responding service found in phase one, keeping the expensive sweep practical. Credentials are given either as separate user and password files (all combinations) or one combined `user:password` file, never both. It is a Python 2-era tool (2008–2010, Midnight Research Labs — the project site is defunct and BlackArch packages the last 0.3a release); the same job today can be scripted from fresh nmap output plus a modern hydra.

## Typical use cases

### Fetch the default-password list

```
depant -g dpl.txt
```

Outcome: downloads the Phenoelit default-password list into dpl.txt as a combined `user:password` file — the ready-made phase-one wordlist for the scans below.

### Dry-run the network mapping

```
depant -H 198.51.100.0/24 -c dpl.txt -D
```

Outcome: `-D` stops after the nmap phase, printing which hosts and services would be tested (the built-in "safe ports" set hydra handles well) without sending any credentials — the sanity check before a real audit.

### Full audit with a second-phase list

```
depant -H 198.51.100.0/24 -e exclude.txt -u users.txt -p passwords.txt -U more-users.txt -P big-dict.txt
```

Outcome: phase one sprays the safe service set with the small lists (hosts in exclude.txt skipped); if nothing is found, phase two attacks only the fastest service with the larger lists. Hits print as `Found user [name] with pass [secret] on [host] service/port [22]`; add `-O results.csv` to log them to a CSV file.

### Widen the port coverage

```
depant -H 198.51.100.10/32 -A -c dpl.txt -O results.csv
```

Outcome: `-A` tests every port hydra knows a service for instead of the safe default set (or name specific ports with `-o 21,22,137-139`), writing any findings to results.csv — expect more errors on unusual services, which `-d` (debug) will surface.

*No corpus evidence — derived from upstream documentation.*
