---
name: "snoopbrute"
categories:
  - blackarch-scanner
version: "17.589fbe6"
homepage: "https://github.com/m57/snoopbrute"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "m57/snoopbrute"
github-stars: 13
github-pushed: "2015-05-11"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# snoopbrute

> Multithreaded DNS cache snooper: fires non-recursive lookups for a wordlist of hostnames at one resolver and reports which names it answers.

## What it's for

snoopbrute (m57, Python 2 with dnspython) targets a single DNS server and asks it — non-recursively, five threads by default — for every hostname in a wordlist. A resolver that answers a non-recursive query is serving that answer from its own cache or zone data, so each hit reveals a hostname its users have recently resolved (cache snooping) or a name the server itself knows; sweeping a wordlist this way doubles as hostname brute force without ever transferring the zone. The wordlist defaults to `domain_wordlist.txt` in the current directory; the package installs a copy under `/usr/share/snoopbrute/`. It tests one resolver per invocation — it is not a recursive-scanning or internet-wide tool.

## Typical use cases

### Snoop a resolver with the bundled wordlist

```
snoopbrute 198.51.100.53 /usr/share/snoopbrute/domain_wordlist.txt
```

Outcome: prints the banner and target resolver, then issues non-recursive A queries for each wordlist entry (5 threads) and reports the hostnames that receive answers — names currently known to that resolver's cache.

### Brute-force internal hostnames with a custom list

```
snoopbrute 198.51.100.53 hostnames.txt
```

Outcome: the same probe over your own list — e.g. naming patterns harvested during the engagement (`ns1`, `intranet`, `mail` prefixed with the target domain) — enumerating resolvable hostnames through the target resolver without an AXFR.

*No corpus evidence — derived from upstream documentation (repo README and script source).*
