---
name: "shortscan"
categories:
  - blackarch-scanner
version: "0.9.2.r0.g2ba744f"
homepage: "https://github.com/bitquark/shortscan"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-05-29"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "bitquark/shortscan"
github-stars: 1237
github-pushed: "2024-11-25"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# shortscan

> IIS 8.3 short-filename enumerator that also recovers the full name via Windows' shortname checksum algorithm.

## What it's for

Shortscan (by bitquark, building on Soroush Dalili's IIS tilde-enumeration
research) exploits the IIS short-name disclosure behavior: on a vulnerable
server, requests that differ only after the `~1` short-name marker return
measurably different responses, letting an attacker walk the alphabet and
learn that files with a given prefix exist — even when directory listing
is off. Shortscan automates that walk and goes further: where a short name
like `SECRET~1` is found it uses Windows' proprietary shortname collision
checksum to compute candidate long filenames and confirm them, often
recovering the full name outright. It recurses into discovered
subdirectories and adapts its detection method (HTTP method magic, status
codes, Levenshtein distance) to the server. It is read-only
reconnaissance against IIS; it does not test anything beyond name
disclosure.

## Typical use cases

### Scan a site for short names

Single-target enumeration.

```
shortscan http://target.example.com/
```

Outcome: confirms the service is vulnerable, enumerates short filenames
at the root, attempts checksum-based autocomplete of each to its full
name, and recurses into subdirectories it finds; confirmed files are
printed with their names (or full URLs with `-F`).

### Batch a list of URLs

Check many sites in one run.

```
shortscan @urls.txt
```

Outcome: the `@` prefix reads one URL per line and scans each in turn,
reporting per-site vulnerability and any recovered filenames — suited to
triaging a large external asset list.

### Vulnerability check only

Skip enumeration when you only need the verdict.

```
shortscan --isvuln http://target.example.com/
```

Outcome: stops as soon as short-name disclosure is confirmed (or ruled
out), costing a handful of requests instead of a full alphabet walk.

### Scan behind a vhost or authenticated route

Reach the right virtual host on a shared IP.

```
shortscan -H 'Host: target.example.com' -H 'Authorization: Basic ***' http://198.51.100.10/
```

Outcome: `-H` (repeatable) attaches arbitrary headers to every probe, so
the scan tests the named virtual host or an authenticated path rather
than the IP's default site.

### Build a custom rainbow table for autocomplete

Improve full-name recovery against known naming conventions.

```
shortutil wordlist project-files.txt > project.rainbow
```

Outcome: `shortutil` (packaged alongside shortscan) computes the
shortname checksum of every wordlist entry into a rainbow table; a later
`shortscan -w project.rainbow http://target.example.com/` matches
observed short names against it to autocomplete names specific to your
target.

*No corpus evidence — derived from upstream documentation (README).*
