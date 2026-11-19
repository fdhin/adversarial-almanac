---
name: "passivedns"
categories:
  - blackarch-sniffer
version: "297.be48a8e"
homepage: "https://github.com/gamelinux/passivedns"
packaged-by: [blackarch]
blackarch-builddate: "2024-06-01"
build-system: autotools
meta-checked: "2026-11-09"
github-repo: "gamelinux/passivedns"
github-stars: 1736
github-pushed: "2026-10-18"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 25
gh-activity-checked: "2026-11-09"
---

# passivedns

> Sniff DNS server responses off the wire or out of pcaps and log them as passive-DNS records.

## What it's for

Passive DNS data lets an investigator ask which address a domain resolved to last week, or which domains shared an IP — questions active resolution can no longer answer. gamelinux's passivedns builds that record: it watches an interface (or reads a pcap file), parses the DNS responses only (IPv4 and IPv6, fragments, DNS over UDP and TCP, HTTPS/SVCB answers included), aggregates duplicates in an in-memory cache, and logs each unique query/answer set with timestamps, TTL and a repeat count. Version 2 writes one JSON object per line by default; `--format text` produces the classic pipe-delimited `||` lines. It can daemonize, chroot, drop privileges and cap its memory, so it doubles as a permanent sensor. It records what DNS servers actually answered — it never sends queries itself.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Run a live passive-DNS sensor

Start collecting DNS intelligence from a monitored link.

```
sudo passivedns -i eth0 -l /var/log/passivedns.log
```

Outcome: every DNS answer seen on eth0 is appended to the log as one JSON line per query/answer set ({"query":"www.example.com","qtype":"A","answer":["198.51.100.10"],"ttl":...,"cnt":5}); duplicate answers are held in the cache and only re-logged after the repeat interval elapses.

### Extract DNS history from a saved capture

Retro-hunt an incident from stored traffic.

```
passivedns -r incident.pcap -l incident-pdns.log
```

Outcome: the same records reconstructed offline from `incident.pcap`; grep the output for a suspicious domain or address to enumerate every name-to-IP mapping observed in that capture, with first/last-seen timestamps.

### Emit classic pipe-delimited lines

Feed tooling that expects the 1.x text format.

```
passivedns -r incident.pcap --format text
```

Outcome: records come out as `timestamp||client||server||IN||query||type||answer||TTL||count` lines to the default log instead of JSON; the delimiter is settable with `-d`, and `-f` selects which fields appear.

### Run it as a hardened daemon

Turn the sensor into a permanent service.

```
sudo passivedns -i eth0 -D -u pdns -g pdns -T /var/lib/passivedns -S 512
```

Outcome: passivedns forks into the background (PID file `/var/run/passivedns.pid`, changed with `-p`), chroots to `/var/lib/passivedns`, drops to the pdns user/group, and caps its cache at 512 MB (`-S`); `-b 'port 53'` adds a BPF filter if the link carries more than DNS.
