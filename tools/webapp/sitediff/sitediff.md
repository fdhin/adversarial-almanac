---
name: "sitediff"
categories:
  - blackarch-webapp
version: "3.1383935"
homepage: "https://github.com/digininja/sitediff"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "digininja/sitediff"
github-stars: 39
github-pushed: "2017-05-16"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sitediff

> Fingerprint a web app by requesting a local source tree's files from the live site and diffing what matches.

## What it's for

Sitediff (Robin Wood / digininja) answers "is this site running the open-source package I think
it is, and at what version?" without touching the package's own files. You point it at a local
directory holding the files of a known application version (the fingerprint source) and at the
target URL; it requests each file from the live site and reports which exist and which match the
local copies. Files that match pin the version; files that differ localize customizations or
version drift. It is a Ruby script driven by getopt-style flags, supports HTTP auth and proxies,
and stops at presence/hash comparison — no content rendering or JS execution.

## Typical use cases

### Compare a local package copy against a live site

```
sitediff -u http://target.example.com -p /opt/app-version-1.2.3/
```

Outcome: each file under the local directory is requested from the target; the report lists
matched, differing and missing files, so the deployed version of the package can be pinned
(mostly matching tree = that version) or shown to be modified.

### Show only the files that match

```
sitediff -u http://target.example.com -p /opt/app-version-1.2.3/ -m
```

Outcome: only successful matches are printed, cutting noise when the goal is simply confirming
"this exact version is deployed".

### Diff an authenticated application

```
sitediff -u http://target.example.com -p /opt/app-version-1.2.3/ --auth_type basic --auth_user user --auth_pass Password1
```

Outcome: requests authenticate with the given credentials (basic or digest), letting the
comparison reach files behind the application's login.

No corpus evidence — derived from the upstream source (sitediff.rb usage) and README.
