---
name: "vnak"
categories:
  - blackarch-voip
version: "1.cf0fda7"
homepage: "https://www.isecpartners.com/vnak.html"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "BlackArch/vnak"
github-stars: 4
github-pushed: "2013-12-22"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# vnak

> VoIP Network Attack Kit — one sniffer-driven tool that injects attacks against IAX, H.323 and SIP conversations passing on the wire.

## What it's for

Attacking live VoIP signalling from a sniffing position. vnak (the "VoIP Network Attack Kit", 2007, GPLv2, written by Zane Lackey of iSEC Partners) combines the commonly used attacks against multiple VoIP protocols into one tool: it passively watches an interface for matching traffic and, when a registration or call passes, injects a forged reply. The built-in attack table covers IAX (authentication downgrade, known authentication challenge, call hangup, call hold/quelch, registration reject), H.323 (registration reject) and SIP (registration reject, call reject, known authentication challenge) — nine attacks, numbered 0-8, selected with `-a`. It must be able to see the victim traffic, so it presumes a hubbed segment or an ARP-spoofed MITM position established with another tool first. It does not fuzz or scan; for SIP-focused auditing see [[sippts]] and for request mutation see [[siparmyknife]].

## Typical use cases

### Attack a specific client-server pair

```
vnak -a 8 198.51.100.10 198.51.100.20
```

Outcome: watches traffic between client 198.51.100.10 and server 198.51.100.20 and, when a matching SIP REGISTER passes, injects the attack-8 forged reply carrying a known authentication challenge; the tool prints `Known Authentication Challenge attack completed successfully against host ...` when it fires — the client's handling of that challenge is the behaviour under test.

### Attack every registration and call on the wire

```
vnak -e -a 6 -i eth0 198.51.100.20
```

Outcome: with `-e` the client address is dropped and every client communicating with the server is attacked — here every SIP registration toward 198.51.100.20 receives a forged rejection (`-i` selects the interface, default eth0). Run `vnak` with no `-a` to print the numbered attack table.

*No corpus evidence — derived from upstream documentation.*
