---
name: "vulmap"
categories:
  - blackarch-scanner
version: "95.a167c47"
homepage: "https://github.com/vulmon/Vulmap"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "vulmon/Vulmap"
github-stars: 975
github-pushed: "2023-03-18"
github-archived: true
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# vulmap

> Local vulnerability scanner that matches installed software against the vulmon.com API for CVEs and downloadable exploits.

## What it's for

Vulmap checks the host it runs on — not a remote target. It inventories installed packages, queries the vulmon.com online API for known vulnerabilities and exploits, and prints CVE IDs, risk scores, detail links and exploit titles for each match. Because it pulls real-time data from the API instead of a local CVE database, even very recent vulnerabilities surface. The project ships two scanners: `vulmap-linux.py` (Python, works on Python 2 and 3, currently limited to dpkg-based distributions) and a PowerShell sibling for Windows; the examples below cover the Linux scanner. The upstream is archived, so expect no further development.

## Typical use cases

### Audit the local host for vulnerable packages

```
python3 vulmap-linux.py
```

Outcome: inventories installed dpkg packages, queries vulmon.com and prints each vulnerability with CVE ID, risk score and a detail link; end with a summary of how many vulnerabilities and exploits were found on this host.

### Show results as they are found

```
python3 vulmap-linux.py -v
```

Outcome: verbose mode — prints each check in real time instead of buffering everything to the end, useful on slow links or when watching the API queries live.

### Download every exploit found during a scan

```
python3 vulmap-linux.py -a
```

Outcome: after the vulnerability listing, downloads all matched exploits (e.g. from Exploit-DB) to the current directory — handy to stage local privilege-escalation proof material.

### Download one specific exploit

```
python3 vulmap-linux.py -d EDB8310
```

Outcome: fetches only the exploit with the given ID (the `EDB...` identifiers come from the scan output or vulmon.com) and saves it locally.

*No corpus evidence — derived from upstream documentation.*
