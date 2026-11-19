---
name: "lsrtunnel"
categories:
  - blackarch-spoof
version: "0.2"
homepage: "http://www.synacklabs.net/projects/lsrtunnel/"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "aur-archive/lsrtunnel"
github-stars: 0
github-pushed: "2015-08-16"
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

# lsrtunnel

> Loose source route tunneling tool — poses as another host's IP by relaying your TCP traffic through a "silent" address using LSRR packets.

## What it's for

lsrtunnel spoofs connections to a remote host using the IP loose source route option. It juggles four addresses: your IP, a silent IP (an unused address on your own subnet that lsrtunnel ARP-claims so packets sent to it reach your interface with no IP stack interfering), the spoofed IP (the identity you pose as — that host never sees a packet), and the target IP (which must honor reverse source routed frames, or nothing works). Point any TCP client at the silent IP and your connections arrive at the target as the spoofed host, inheriting its apparent identity and privileges. Must run as root; needs libpcap and libdnet. Modern networks commonly filter source-routed packets, which limits where this works, and interactive SSH logins are a known failure case while telnet/rsh/rlogin behave normally.

## Typical use cases

### Spoof a connection to a target through a silent IP

```bash
sudo lsrtunnel -i 198.51.100.20 -t 198.51.100.50 -f 198.51.100.99
```

Outcome: a child process starts answering ARP for the silent IP (198.51.100.20) with your interface's MAC, so the local host stops ARPing for it. Then point a TCP client at the silent IP: packets you send there are repackaged as coming from the spoofed IP (198.51.100.99), loose-source-routed through the silent IP toward the target (198.51.100.50); replies to the spoofed IP are translated back to you. The target's logs show the spoofed address.

*No corpus evidence — derived from upstream documentation.*
