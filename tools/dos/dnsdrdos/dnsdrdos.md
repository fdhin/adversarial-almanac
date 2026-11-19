---
name: "dnsdrdos"
categories:
  - blackarch-dos
version: "0.1"
homepage: "http://nullsecurity.net/tools/dos.html"
packaged-by: [blackarch]
blackarch-builddate: "2015-02-01"
build-system: other
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "nullsecuritynet/tools"
github-stars: 70
github-pushed: "2026-10-13"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-09"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 36
gh-activity-checked: "2026-11-09"
---

# dnsdrdos

> noptrix's proof-of-concept for distributed DNS reflection DoS — one spoofed query per nameserver, all answers land on the victim.

## What it's for

dnsdrdos demonstrates distributed DNS reflection denial of service: it sends a DNS request to every server in a user-supplied list of nameservers using a spoofed source address, so the servers' replies — the amplification — are delivered to the chosen victim rather than to the sender. All it needs is a list of authoritative nameservers (one IP per line); the queried domain, the spoofed address and the loop count are configurable, and everything else is fixed. It is quick-and-dirty PoC code from the nullsecurity team (2010) for a well-known technique: use it to study reflection behavior and test amplification mitigations in an isolated lab network. It must run as root because it forges raw IP/UDP packets.

### Reflect DNS answers at a victim from a server list

The core PoC run: forged queries, spoofed source, counted loops.

```
sudo ./dnsdrdos -f nameserver.lst -s 198.51.100.10 -d example.com. -l 10000
```

Outcome: for 10,000 loops, a DNS query for `example.com.` is sent to every IP in `nameserver.lst` with source address 198.51.100.10; the nameservers' replies are directed at that address. On the sending host you observe only the outgoing queries — the effect lands on the spoofed host, so run a capture there to count reflected answers. Requires root.

### Rotate the queried name each round

Defeats caches and per-name rate limits by never asking the same question twice.

```
for i in $(seq 1 1000); do sudo ./dnsdrdos -f nameserver.lst -s 198.51.100.10 -d $i.example.com. -l 1000; done
```

Outcome: repeats the flood with a fresh subdomain per round (the upstream-recommended pattern), so every resolver query misses cache and each nameserver must generate a new answer — sustained reflection volume stays high across rounds.

*No corpus evidence — derived from upstream documentation.*
