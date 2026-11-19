---
name: "gooscan"
categories:
  - blackarch-automation
version: "1.0.9"
homepage: "http://johnny.ihackstuff.com/downloads/task,doc_details&Itemid=/gid,28/"
packaged-by: [blackarch]
blackarch-builddate: "2025-11-30"
build-system: other
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "aur-archive/gooscan"
github-stars: 0
github-pushed: "2015-08-15"
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

# gooscan

> Classic C utility by Johnny Long that automates queries against Google and authorized Google Search Appliances.

## What it's for

gooscan (j0hnny, c. 2005) automates Google queries in bulk: a single query or a whole file of them, a target Google server or Google Search Appliance (GSA), optional site restriction, proxy bouncing, hex-encoding of non-alphanumeric characters to slip past filters, and HTML-formatted result output. The "twist" in its design is appliance support — extra request fields that only GSAs accept. It performs no exploitation; it is a recon/dorking front end. Its original homepage is long gone (BlackArch ships the C source directly), and the tool itself warns that running it against www.google.com violates Google's terms of service — point it at appliances or search infrastructure you are authorized to test.

## Typical use cases

### Run a dork against an authorized search appliance

```
gooscan -t search.example.com -q "intitle:index.of backups" -o results.html
```

Outcome: the query is submitted to the appliance and matches are written as an HTML-formatted list to `results.html` for review.

### Work through a dork list via a proxy

```
gooscan -t search.example.com -i queries.txt -p 198.51.100.10:3128 -v
```

Outcome: every query in the file is run in turn through the HTTP proxy, with verbose progress on stderr and results accumulated to the output file.

### Scope to one site with filter evasion

```
gooscan -t search.example.com -q "confidential" -s target.example.com -d -v
```

Outcome: results are restricted to `target.example.com` (`-s`) and all non-alphabetic characters in the query are hex-encoded (`-d`) to dodge simple input filters, with verbose output during the run.

*No corpus evidence — derived from upstream documentation.*
