---
name: "nmap_vulscan"
gentoo-category: "net-analyzer"
version: "1.0-r1"
homepage: "http://www.scip.ch/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "scipag/vulscan"
github-stars: 3794
github-pushed: "2026-02-06"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-09"
worth: 5.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# nmap_vulscan

> Nmap NSE module that turns `-sV` version detection into an offline vulnerability lookup across bundled databases (VulDB, CVE, Exploit-DB, and more).

## What it's for

vulscan is an Nmap script that rides on version detection: whatever product and version `nmap -sV` identifies on a port is looked up in local CSV vulnerability databases shipped with the module (scipvuldb/VulDB, cve.csv, securityfocus, exploitdb, openvas, osvdb, securitytracker, xforce), so a version scan doubles as a vulnerability assessment with no exploit traffic sent. It is inference, not verification — matches depend entirely on nmap's version guess and database coverage, and version-less entries (Apache especially) generate false positives. Treat hits as leads for follow-up, not confirmed flaws. The module installs under the Nmap scripts directory (on Linux typically `/usr/share/nmap/scripts/vulscan/`).

## Typical use cases

### Basic vulnerability scan

```
nmap -sV --script=vulscan/vulscan.nse target.example.com
```

Outcome: for every service where version detection succeeds, the per-port output gains a ` vulscan ` section listing matching vulnerability IDs and titles from all bundled databases.

### Query a single database

```
nmap -sV --script=vulscan/vulscan.nse --script-args 'vulscandb=cve.csv' target.example.com
```

Outcome: only the named database file is consulted, which cuts duplicate hits when you want one clean list per source (any custom CSV with `id;title` rows works too).

### Change the report layout

```
nmap -sV --script=vulscan/vulscan.nse --script-args 'vulscanoutput=details' target.example.com
nmap -sV --script=vulscan/vulscan.nse --script-args "vulscanoutput=ID: {id} - {title} ({matches})" target.example.com
```

Outcome: results print in the chosen format — predefined `details`, `listid`, `listlink`, `listtitle`, or a custom template over `{id}`, `{title}`, `{matches}`, `{product}`, `{version}`, `{link}`.

### Show every possible match

```
nmap -sV --script=vulscan/vulscan.nse --script-args 'vulscanshowall=1' target.example.com
```

Outcome: instead of only best matches, every database entry that matches the product name is printed — noisier, but useful when hunting a specific version range.

### Refresh the vulnerability databases

```
sudo /usr/share/nmap/scripts/vulscan/update.sh
```

Outcome: downloads fresh copies of the database CSVs from upstream into the vulscan directory; run it before assessments so the lookups cover recently disclosed vulnerabilities.

*No corpus evidence — derived from upstream documentation (README of scipag/vulscan).*
