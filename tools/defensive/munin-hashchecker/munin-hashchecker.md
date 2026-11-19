---
name: "munin-hashchecker"
categories:
  - blackarch-defensive
version: "260.cf6cbe9"
homepage: "https://github.com/Neo23x0/munin"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-28"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "Neo23x0/munin"
github-stars: 859
github-pushed: "2026-11-06"
github-archived: false
github-issues: 28
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 25
gh-activity-checked: "2026-11-09"
---

# munin-hashchecker

> Batch-enriches hash and IOC lists against VirusTotal and eight other online lookup services.

## What it's for

Munin (by Florian Roth) is an online hash checker: given a file of hashes — any text containing hashes works, the position and surrounding comment are auto-detected — a CSV, or a folder of samples, it queries VirusTotal, Hybrid Analysis, Any.run, URLhaus, MISP, CAPE, MalShare, Valhalla and Hashlookup, and consolidates the verdicts into a CSV for reporting. Results are cached in a JSON database so a hash appearing many times in one batch is queried once; AV hits from a user-defined vendor list are highlighted; and in-batch extras flag imphash duplicates and repeated PE signers, which exposes shared toolkits. API keys live in an ini file passed with -i. Besides batch mode it can pull hashes from Valhalla by keyword, YARA rule or ATT&CK technique, run interactively (--cli), or serve checks over HTTP (--web); VirusTotal public-API quota applies, and --vtwaitquota makes it wait for the next day instead of aborting. The packaged `munin`/`munin-host` commands wrap munin.py and munin-host.py.

## Typical use cases

### Check every hash in a list

```
munin -i my.ini -f hashes.txt
```

Outcome: looks up each hash across the configured services, reuses cached answers from vt-hash-db.json for repeats, prints the enriched results, and appends them to a dated CSV (detections, wild filenames, signer, first/last submission).

### Triage a folder of samples

```
munin -i my.ini -s samples/case34
```

Outcome: hashes every file in samples/case34 and checks those hashes online, producing the same CSV verdict report as hash-list processing — physical sample triage without submitting the files anywhere.

### Pull hashes for an ATT&CK technique from Valhalla

```
munin -i my.ini --vh T1023
```

Outcome: queries Valhalla for hashes tied to the keyword, YARA rule name, ATT&CK software/technique or threat group, then checks each retrieved hash against the other services exactly like a normal batch.

### Serve lookups over HTTP for other tools

```
munin -i my.ini --web -w 8080
```

Outcome: starts a web service on port 8080; requesting http://host:8080/<string> checks any hash-bearing string passed in the URL and returns the enriched result — a lightweight enrichment endpoint for playbooks and automation.

### Enrich IP and domain IOC lists

```
munin-host -i my.ini -f iocs.txt
```

Outcome: the companion checker extracts IP addresses and hostnames from iocs.txt, retrieves VirusTotal context for each (associated files and URLs, capped by -m), optionally resolves domains (--noresolve skips it) or processes resolved IPs too (--recursive), and writes a CSV like the hash checker.
