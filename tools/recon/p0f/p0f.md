---
name: "p0f"
categories:
  - information-gathering
version: "3.09b-3"
homepage: "http://lcamtuf.coredump.cx/p0f3/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2020-07-07"
build-system: make
license: GPL2
meta-checked: "2026-11-09"
github-repo: "p0f/p0f"
github-stars: 521
github-pushed: "2019-07-09"
github-archived: false
github-issues: 11
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# p0f

> Michal Zalewski's classic passive fingerprinter: identifies the OS and software behind TCP/IP traffic without generating any traffic of its own.

## What it's for

p0f v3 fingerprints the players behind incidental TCP/IP communications using purely passive techniques — often from a single observed SYN packet. It identifies the operating system on both endpoints of a connection (useful exactly where active probes like NMap are blocked, slow, or would trip alarms), estimates network distance and uptime, detects connection sharing/NAT, load balancers and application-level proxies, and can flag dishonest clients that forge declarative headers like User-Agent or X-Mailer by contradicting what their stack actually reveals. It runs in the foreground or as a daemon, can fingerprint from saved pcap captures instead of live traffic, offers a real-time API over a Unix socket so other tools can query verdicts, and writes a grep-friendly log. Reach for it during reconnaissance on networks you monitor or are authorized to assess; it sends nothing toward the target, which is both its strength (undetectable) and its limit (it can only see traffic that crosses the capture interface).

## Typical use cases

### Passively fingerprint live traffic on an interface

Listen on a network interface and print verdicts as connections happen.

```
p0f -i eth0
```

Outcome: for each observed connection p0f prints a boxed record (e.g. `client = 198.51.100.10`, `os = Windows XP`, `dist = 8`, `raw_sig = ...`) for SYN and SYN+ACK directions — the OS on both ends, distance and NAT hints, with zero packets transmitted.

### Run as a daemon with a persistent log

Background it, drop privileges, and keep a grep-friendly record.

```
p0f -i eth0 -d -u p0f-user -o /var/log/p0f.log
```

Outcome: p0f forks into the background (`-d`), drops to the unprivileged user, and appends one flat log line per verdict to `/var/log/p0f.log`, suitable for `grep`/SIEM ingestion over long monitoring windows.

### Fingerprint a saved capture

Work offline against pcap files instead of a live interface.

```
p0f -r capture.pcap
```

Outcome: p0f reads the capture and prints the same per-connection fingerprint boxes for traffic already recorded — useful for incident response triage on handed-over pcaps; combine with `-o` to also emit the flat log.

### Expose verdicts to other tools over the API socket

Serve third-party queries in real time.

```
p0f -i eth0 -s /tmp/p0f.sock -d
```

Outcome: p0f listens for API queries on `/tmp/p0f.sock`; clients send an address and receive a structured verdict (status, OS, distance) per the documented binary protocol, letting daemons such as mail or web frontends tag connections with p0f's intelligence on the fly.

> No corpus evidence — derived from upstream documentation.
