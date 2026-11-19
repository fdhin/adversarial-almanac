---
name: "wafp"
categories:
  - blackarch-webapp
version: "0.01_26c3"
homepage: "http://packetstormsecurity.com/files/84468/Web-Application-Finger-Printer.01-26c3.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-19"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "Seabreg/wafp"
github-stars: 0
github-pushed: "2019-01-20"
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

# wafp

> Ruby web-application finger printer that pins exact app versions — down to build numbers — by matching MD5 checksums of known files.

## What it's for

WAFP (Web Application Finger Printer, by Richard Sammet "e-axe", 26C3 release) identifies the detailed version of a web application running on a server — even the build number — by fetching the files listed in its SQLite fingerprint database (fprints_wafp.db) and comparing MD5 checksums; when checksums do not match uniquely, a scoring system ranks the most probable versions. If you do not know which product the site runs, auto-identify mode (the default, no -p needed) determines it from the same file and directory structure first. Fingerprints for new applications can be generated from any install tarball with the bundled generate_wafp_fingerprint.sh, and remote copies can be fetched-and-stored for later offline fingerprinting. A 2009-era tool written for Ruby 1.8 with SQLite3.

## Typical use cases

### Auto-identify the product and version

One-argument fingerprinting.

```
wafp http://target.example.com/board
```

Outcome: identifies which application drives the path, then fingerprints its detailed version/build from file checksums and prints the ranked version matches.

### Fingerprint a known product with a version constraint

Skip identification when you already know the app.

```
wafp -p 'wordpress' -v '2%' http://target.example.com/
```

Outcome: tests only WordPress fingerprints in the 2.x series (the % is a SQL-style wildcard for version patterns), printing the closest matching versions — faster and quieter than auto-identify.

### Fetch and store a copy for offline fingerprinting

Two-phase scanning.

```
wafp -f -t 32 -s phpmy-save01 -p 'phpmyadmin' -v '1.1.%' https://user:Password1@target.example.com/phpmyadmin/
```

Outcome: fetches all fingerprint files for phpMyAdmin 1.1.x (32 threads, HTTP basic auth in the URL) into the stored archive phpmy-save01 without fingerprinting (-f); later `wafp -d phpmy-save01 -p 'phpmyadmin' -v '1.1.%'` re-runs the fingerprint offline against the stored data.

### List products with available fingerprints

See what the database knows.

```
wafp -P '%bb%'
```

Outcome: prints every product in the fingerprint database whose name contains "bb"; '%' alone dumps the complete product list — useful before a targeted -p scan.

No corpus evidence — derived from the packaged upstream source (README/HOWTO and wafp.rb usage of WAFP 0.01-26c3; Seabreg/wafp is a mirror of the same release).
