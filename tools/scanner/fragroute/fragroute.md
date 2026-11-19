---
name: "fragroute"
categories:
  - scanner
version: "1.2"
homepage: "http://www.monkey.org/~dugsong/fragroute/"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "ajkeeton/fragroute"
github-stars: 4
github-pushed: "2015-12-11"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fragroute

> Dug Song's IDS-evasion rewriter: it intercepts your own outbound traffic to one target host and rewrites it — fragmenting, overlapping, chaffing, reordering — to test whether a network IDS reassembles it the way the destination does.

*No corpus evidence — derived from upstream documentation.*

## What it's for

fragroute implements most of the insertion/evasion attacks from the Secure Networks paper "Insertion, Evasion, and Denial of Service: Eluding Network Intrusion Detection" (January 1998) as a ruleset-driven traffic rewriter. You point it at a destination host and it sits on your egress path, transforming everything you send: delaying, duplicating or dropping packets, splitting them into IP fragments or tiny TCP segments (optionally with overlaps that favor old or new data), interleaving chaff, spoofing TTLs or options, and shuffling order. If the IDS reconstructs fragments and streams differently from the victim host, it misses the traffic — which is exactly what you are testing. Scope matters: unlike its predecessor [[fragrouter]] (the one-way routing engine from the same author's nidsbench suite, which forwarded other machines' traffic), fragroute only rewrites packets originating from the local machine, and IP forwarding must stay off while it runs. Its sibling `fragtest` checks how a target host itself handles fragmentation and TTL edge cases.

## Typical use cases

### Rewrite traffic to a host with the default ruleset

```
sudo fragroute 198.51.100.10
```

Outcome: all locally originated traffic to that host now passes through the default ruleset at `/usr/local/etc/fragroute.conf` — 1-byte TCP segments favoring newer data, 24-byte IP fragments, duplicate chaff, random ordering, and a tcpdump-style print of every packet — a ready-made stress test for any IDS on the path.

### Load a custom ruleset

```
sudo fragroute -f frag.conf 198.51.100.10
```

Outcome: your ruleset drives the pipeline instead. The manpage's worked example is `ip_frag 8 old` + `order random` + `print`: forward-overlapping 8-byte fragments that favor older data, shuffled delivery, echoed to the terminal — a minimal, precise evasion profile to compare what the IDS logs against what the target actually receives.
