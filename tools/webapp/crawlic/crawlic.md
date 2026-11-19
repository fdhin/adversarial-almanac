---
name: "crawlic"
categories:
  - blackarch-webapp
version: "51.739fe2b"
homepage: "https://github.com/Ganapati/Crawlic"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Ganapati/Crawlic"
github-stars: 105
github-pushed: "2016-11-22"
github-archived: true
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# crawlic

> One-shot web recon sweep: robots.txt, temp/backup files, folder guessing, dorks and co-hosted domains.

## What it's for

Crawlic runs a selectable set of recon techniques against one site and prints everything it finds: entries parsed from robots.txt, temporary/backup files (`~`, `.bak`, editor remnants), common folder names, Google dorks, and reverse-DNS lookups (via Bing) for domains hosted on the same server. As a bonus, if it encounters an exposed `.git` or `.svn` directory it can automatically rip the repository with dvcs-ripper. Techniques are chosen with `-t` from the letters `r` (robots), `t` (temp files), `f` (folders), `g` (google dorks), `d` (reverse dns); default is all five. It is a 2016 Python 2 tool from an archived repository — behavior and bundled lists (folders.lst, dorks.lst, google_dorks, all editable) reflect that era.

## Typical use cases

### Run the full recon sweep

All techniques against one target.

```
crawlic -u http://target.example.com/
```

Outcome: runs robots.txt parsing, temp-file probing, folder guessing, Google dorks and reverse-DNS in sequence, printing each discovery (`[!] /hidden_file.php`, `[!] http://target.example.com/admin/`) under a per-technique header — a fast first-pass map of what the site leaks.

### Pick specific techniques

Only the checks you want, in the order you name them.

```
crawlic -u http://target.example.com/ -t rtf
```

Outcome: restricts the run to robots.txt (`r`), temporary files (`t`) and folders (`f`) — skipping dorks and reverse-DNS when search-engine noise or speed matters; any subset like `rg` or `d` works the same way.

No corpus evidence — derived from upstream documentation.
