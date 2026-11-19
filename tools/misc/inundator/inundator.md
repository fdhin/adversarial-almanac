---
name: "inundator"
categories:
  - blackarch-misc
version: "0.5"
homepage: "http://inundator.sourceforge.net/"
packaged-by: [blackarch]
blackarch-builddate: "2019-11-16"
build-system: perl
license: BSD
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# inundator

> Fill a target's IDS/IPS logs with false positives so a real attack gets buried in alert noise.

## What it's for

inundator is a Perl tool for IDS-evasion testing: it parses local Snort rules files and crafts innocent packets/requests that match those rules, flooding intrusion detection logs with false positives (a measured ~90% trigger rate). It is multithreaded and queue-driven, nmap-scans targets to find open ports before sending, and routes everything through a SOCKS proxy (Tor on localhost:9050 by default) so the noise is not attributable to your address. The intended workflow is to start it shortly before an authorized engagement, let it run during, and keep it running after — the goal being that the analyst drowns in alerts. It does not exploit anything; it only trips signatures. Use it strictly against systems you are authorized to test.

## Typical use cases

### Bury activity in alerts via Tor

Generate false positives against one target with the defaults (Tor SOCKS on localhost:9050, `/etc/snort/rules`, 25 threads).

```
inundator target.example.com
```

Outcome: after an nmap port scan of the target, worker threads start sending rule-matching junk requests through Tor; the target's IDS console fills with alerts while inundator itself stays quiet unless connections fail. A single host, an IP range (`198.51.100.10-198.51.100.25`), or a CIDR subnet are all accepted as targets.

### Point it at a faster SOCKS proxy

Scale up threads and use an authenticated proxy instead of Tor.

```
inundator -p socks.example.com:1080 -a user:Password1 -t 50 198.51.100.0/24
```

Outcome: attacks against the whole /24 are sent through the authenticated SOCKS5 proxy with 50 concurrent threads; if the proxy or local link saturates, add `-d 100000` (a per-thread microsecond delay between attacks) and lower the thread count.

### Use a custom rules set and watch progress

```
inundator -r /etc/snort/rules/ --verbose 198.51.100.10
```

Outcome: rules are parsed from the given directory (add `-u` to also use commented-out rules) and verbose mode prints running success/failure connection counts so you can confirm the flood is actually landing.

*No corpus evidence — derived from the packaged source. Upstream is the SourceForge project; beware the same-named GitHub repos `opsengine/inundator` and `Seabreg/inundator`, which are an unrelated HTTP throughput benchmark.*
