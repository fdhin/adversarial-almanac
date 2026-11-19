---
name: "fpdns"
categories:
  - blackarch-fingerprint
version: "109.655bfa7"
homepage: "https://github.com/kirei/fpdns"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: BSD
meta-checked: "2026-11-09"
github-repo: "kirei/fpdns"
github-stars: 117
github-pushed: "2024-09-19"
github-archived: true
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fpdns

> DNS server software fingerprinter — interrogates a nameserver with crafted queries and deduces which DNS implementation and version it runs, even when `version.bind` is disabled.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Remotely identifying the DNS software behind an address — BIND, NSD, Unbound, PowerDNS, Windows DNS and kin — as input to vulnerability assessment of nameservers. fpdns (Perl, by Roy Arends and Jakob Schlyter) sends a series of probe queries and infers the implementation from behavioral differences in the responses, rather than trusting the CH TXT `version.bind` answer (which hiding is common; `-f` can force that query explicitly anyway). It accepts one or more servers on the command line, all authoritative servers of a domain (`-D`), or a list on stdin for bulk scans fanned out over parallel workers. Upstream states the tool is not kept up to date and is likely to produce unreliable results — current DNS software may fall outside its signature tables, so treat verdicts as a lead, not proof.

## Typical use cases

### Fingerprint a nameserver

Identify the DNS implementation at one address.

```
fpdns 198.51.100.10
```

Outcome: sends probe queries to 198.51.100.10:53 and prints a verdict line of the form `fingerprint (198.51.100.10, 198.51.100.10): BIND 9.x` naming the detected software and version range.

### Check every authoritative server of a domain

Fingerprint a domain's whole serving infrastructure in one command.

```
fpdns -D example.com
```

Outcome: resolves example.com's NS records and fingerprints each authoritative nameserver in turn, printing one verdict per server address.

### Bulk-fingerprint from a list

Scan a scope of resolvers collected elsewhere.

```
fpdns - < nameservers.txt
```

Outcome: reads one server per line from stdin and fingerprints them concurrently (10 parallel workers by default, adjustable with `-F`), printing one verdict line per server.
