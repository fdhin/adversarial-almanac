---
name: "synscan"
categories:
  - blackarch-scanner
version: "5.02"
homepage: "https://www.digit-labs.org/files/tools/synscan/"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-12"
build-system: autotools
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "Seabreg/synscan"
github-stars: 1
github-pushed: "2019-03-22"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# synscan

> Digit-labs' high-speed asynchronous half-open TCP port scanner.

## What it's for

Synscan (John Anderson and Neil Kettle, digit-labs.org; GPL v2; release
5.02) is a raw-packet SYN port scanner built for speed: it transmits TCP SYNs
to arbitrary address and port blocks as fast as the interface and kernel
allow — large class-A sweeps in minutes — while its companion `sslog` sniffs
the returning SYN/ACK packets to determine which ports are actually open.
Burst length, inter-burst delay, interface and IP flags are all tunable, and
target blocks can come from the command line or a file. It occupies the same
niche as masscan: built for very large ranges where `nmap -sS` would take too
long, overkill for a single host, and its output is a stream of open
host:port pairs rather than service banners.

## Typical use cases

### Sweep a block with the paired listener

Start the receiver, then blast SYNs.

```
sslog -I eth0 -f open-ports.txt &
synscan -I eth0 -b 198.51.100.0/24 -p 22,80,443
```

Outcome: synscan transmits SYNs across the /24 for the listed ports while
sslog captures the SYN/ACK replies, printing each open host:port as it
arrives and logging findings to open-ports.txt.

### Tune rate and read blocks from a file

Throttle for fragile links and bigger ranges.

```
synscan -I eth0 -f blocks.txt -p 22,80 -B 20 -T 1000
```

Outcome: network blocks are read one per line from blocks.txt instead of the
command line, and packets go out in bursts of 20 (`-B`) with 1000 usec
between bursts (`-T`) — a gentler pace for networks that drop aggressive
traffic.

*No corpus evidence — derived from the upstream project page
(digit-labs.org/files/tools/synscan) and the packaged synscan-5.02 source
(print_usage in synscan.c and sslog.c).*
