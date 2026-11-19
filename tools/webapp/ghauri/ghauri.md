---
name: "ghauri"
categories:
  - blackarch-webapp
version: "1.4.3.r0.g18e3677"
homepage: "https://github.com/r0oth3x49/ghauri"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "r0oth3x49/ghauri"
github-stars: 4088
github-pushed: "2025-10-04"
github-archived: false
github-issues: 25
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ghauri

> Cross-platform SQL injection detection and exploitation tool in the sqlmap lineage, tuned for injections sqlmap misses.

## What it's for

Ghauri automates detection and exploitation of SQL injection flaws — boolean-based blind, error-based, time-based blind and stacked queries — against MySQL, Microsoft SQL Server, Postgres, Oracle and Microsoft Access. It accepts a raw URL, a full HTTP request file (for injections behind authentication), or a bulk target list, then enumerates and dumps the back-end database with automatic technique switching and bypasses. It does not yet support union-query injection (an upstream TODO). Reach for it when an obviously injectable parameter survives a sqlmap run.

## Typical use cases

### Enumerate databases behind an injectable GET parameter

Point it at the URL with the suspect parameter and enumerate.

```
ghauri -u "http://target.example.com/product.php?id=1" --dbs
```

Outcome: fingerprints the DBMS, confirms the injection, and prints the database list (add `--banner --current-user` for version and login context), keeping session state so phases can be resumed.

### Exploit a request captured behind authentication

Save the full vulnerable HTTP request to a file and let ghauri replay it.

```
ghauri -r request.txt --batch --dump -D shop -T customers
```

Outcome: replays the request with its headers and cookies, confirms the injection in the captured parameter, and dumps the `customers` table of the `shop` database without further prompts.

### Tune detection depth and request rate

Control how many parameter locations are tested and how hard the target is hit.

```
ghauri -u "http://target.example.com/page?id=1" --level 3 --threads 4 --delay 1
```

Outcome: level 3 additionally tests header and cookie parameters, while 4 concurrent requests throttled by a 1-second delay keep the scan steady against fragile hosts.

No corpus evidence — derived from upstream documentation.
