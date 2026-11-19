---
name: "grabbb"
categories:
  - blackarch-scanner
version: "0.0.7"
homepage: "https://packetstormsecurity.com/files/11372/grabbb.0.7.tar.gz.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: other
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Mazuco/grabbb"
github-stars: 0
github-pushed: "2017-10-09"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# grabbb

> Minimal, very fast banner grabber: sweeps IP ranges on one or more ports and prints the first line each daemon sends.

## What it's for

grabbb (scut of teso) does one thing: connect across an address range on single or multiple
ports and capture the first line the remote daemon volunteers — useful for inventorying
SSH/FTP/SMTP/NNTP-style banners at statistical scale. It works for "simple" protocols that greet
the client on connect; protocols that wait for the client to speak first (telnet) are out of
scope. It is pure recon: no parsing beyond the banner, no exploiting. Targets come from an IP
range on the command line or a file of raw addresses, and anything piped on stdin is written to
every successful connection before the banner is read.

## Typical use cases

### Sweep a range for banners

Capture what answers across a whole subnet on the interesting ports.

```
grabbb -a 198.51.100.1 -b 198.51.100.254 21 22 25
```

Outcome: connects to every host in the range on each listed port and prints the address of each
hit with the first line its daemon sends; add `-v` for progress and `-s` for a summary after the
scan.

### Read targets from a file

Banner-grab a prepared target list instead of a contiguous range.

```
grabbb -i targets.txt 445
```

Outcome: same banner capture for every raw IP listed in `targets.txt` (IPs only, no hostnames),
one line per responding service.

### Capture a full multi-line greeting

Keep reading past the first line for protocols that greet in several lines.

```
grabbb -m -a 198.51.100.1 -b 198.51.100.254 25
```

Outcome: multiline mode (`-m`) prints every line the daemon sends before going quiet, capturing
complete SMTP-style greetings that the default first-line mode would truncate.

*No corpus evidence — derived from upstream source (grabbb 0.0.7 `usage()` in grabbb.c and the
packaged README; original by scut of teso, distributed via Packet Storm).*
