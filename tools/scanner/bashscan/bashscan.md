---
name: "bashscan"
categories:
  - blackarch-scanner
version: "94.80c066c"
homepage: "https://github.com/astryzia/BashScan"
packaged-by: [blackarch]
blackarch-builddate: "2021-06-29"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "astryzia/BashScan"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# bashscan

> Pure-bash port scanner built on /dev/tcp — network and service discovery on systems where nmap cannot be installed or run: restricted shells, minimal containers, stripped production images.

*No corpus evidence — derived from upstream documentation (the upstream repository has since been removed from GitHub; the archived README is the source).*

## What it's for

Scanning TCP ports when the only tool guaranteed present is bash itself. BashScan uses the shell's `/dev/tcp` pseudo-device for connect scans, so it needs no compiled binaries, no libraries and no root (though host discovery improves with it: `-r/--root` forces the ARP ping even unprivileged). It speaks enough nmap idiom to be comfortable — top-N ports, timing templates 0-6, port lists and ranges, banner grabbing — plus a monolithic `bashscan.sh` form for dropping onto a target as a single file. It is TCP-connect only: no SYN scan, no OS fingerprinting, no script engine. The upstream repository is gone; the BlackArch package and the archived README document the last released behavior.

## Typical use cases

### Quick top-ports scan with banners

```
bashscan --top-ports 100 -T 5 -b 198.51.100.10
```

Outcome: TCP-connect scan of the 100 most common ports (default top-ports is 20) at timing template 5 (0-6, default 4), grabbing service banners from open ports — enough to profile a host from a shell where nmap is unavailable.

### Scan an explicit port list

```
bashscan -r -p 22,80,443,3306,3389 -b 198.51.100.10
```

Outcome: scans exactly the listed ports, reporting open/closed and banners; `-r` forces the ARP-ping host discovery step to run even though the shell lacks root privileges.

### Sweep a host range

```
bashscan --root --timing 6 --banner -p 22 198.51.100.1-254
```

Outcome: sweeps the range for port 22 at the fastest timing template, printing open hosts and their SSH banners — the trailing `-1-254` range applies to the final octet of the address.
