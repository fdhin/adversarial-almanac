---
name: "xpire-crossdomain-scanner"
categories:
  - blackarch-scanner
version: "1.0cb8d3b"
homepage: "https://github.com/mandatoryprogrammer/xpire-crossdomain-scanner"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "mandatoryprogrammer/xpire-crossdomain-scanner"
github-stars: 26
github-pushed: "2015-08-04"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# xpire-crossdomain-scanner

> Audits Flash `crossdomain.xml` policies for allow-access-from entries that point at expired, registrable domains.

## What it's for

A `crossdomain.xml` policy that trusts a domain nobody owns anymore is a takeover vector: register the expired domain and the policy grants your content the privileges meant for the original owner. This scanner (mandatoryprogrammer, 2015) fetches each target's `crossdomain.xml`, extracts every permitted domain, and checks whether those domains have expired — the same dangling-reference idea as its sibling tool [[xcname]], applied to Flash policies instead of CNAMEs. Targets can be given directly or loaded in bulk from Alexa-top-sites-format CSV files, results can go to an HTML spreadsheet, and scanning order can be randomized to spread the load. It is a Python 2 PoC-era tool: the takeover class it hunts has faded with Flash, but the audit logic still applies to any surviving policy-driven trust file.

## Typical use cases

### Check specific domains directly

```
./crossdomain-scan.py -d example.com target.example.com
```

Outcome: fetches `/crossdomain.xml` from each named host, parses the allow-access-from entries and reports which trusted domains are expired — findings print with the `[*]`/`[!]` log prefixes.

### Audit a whole top-sites dataset

```
./crossdomain-scan.py -a top-1m.csv
```

Outcome: reads domains from Alexa-format CSV file(s) and runs the same policy check across them — the bulk mode the tool was written for; pair with `-r` to randomize host order.

### Produce a spreadsheet of vulnerable domains

```
./crossdomain-scan.py -a top-1m.csv -x
```

Outcome: in addition to console output, generates an `.xls` spreadsheet listing the domains whose policies trust expired names — the deliverable form for reporting a sweep.

### Tolerate slow hosts and see progress

```
./crossdomain-scan.py -d example.com -t 10 -V
```

Outcome: raises the HTTP timeout from the default 3 seconds for slow policies/CDNs and enables verbose logging so every request and parse step is visible.

*No corpus evidence — derived from upstream documentation.*
