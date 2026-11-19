---
name: "sniffles"
categories:
  - blackarch-networking
version: "469.118e93f"
homepage: "https://github.com/petabi/sniffles"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-15"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "petabi/sniffles"
github-stars: 66
github-pushed: "2021-02-25"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sniffles

> Generates pcaps of rule-matching, almost-matching and random traffic to stress-test IDS and regular-expression engines.

## What it's for

Sniffles builds synthetic packet captures from Snort rule files or plain regular-expression files: it converts each rule or regex into an NFA and walks a random path through it, so the generated content provably matches (full match), nearly matches (partial match — the classic deep-packet-inspection worst case), or is pure random background traffic. It can also emit evaluation pcaps with exactly one packet per rule (or per regex branch) for verifying that every rule fires as expected, and it models realistic TCP sessions — handshakes, ACKs, teardowns, correct sequence numbers and checksums. The output is a standard pcap you replay at an IDS; Sniffles neither sniffs nor replays live traffic itself.

## Typical use cases

### Generate traffic that matches every rule

```
sniffles -c 10 -f myrules.rules -m
```

Outcome: writes a pcap of 10 streams whose payload content fully matches rules from the file (regex files `.re` work the same way) — worst-case input for measuring IDS inspection throughput.

### Generate almost-matching traffic

```
sniffles -c 10 -f myrules.rules
```

Outcome: a pcap where each stream's content nearly matches a rule (the last matching character erased) — near-worst-case load that never quite triggers the rule.

### Produce one packet per rule to verify coverage

```
sniffles -f myrules.rules -e
```

Outcome: an evaluation pcap containing exactly one matching packet per rule, in sequence; any rule that does not alert on its own packet indicates a rule or engine mismatch worth investigating.

### Generate realistic random background traffic

```
sniffles -c 10 -p 5 -l 50 -t -T -a
```

Outcome: a pcap of 10 streams, 5 packets each, with ACKs, handshake and teardown plus 50-byte data payloads — best-case random traffic for baselining IDS speed.

*No corpus evidence — derived from upstream documentation.*
