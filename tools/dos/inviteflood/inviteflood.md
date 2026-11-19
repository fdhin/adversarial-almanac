---
name: "inviteflood"
categories:
  - blackarch-dos
version: "2.0"
homepage: "https://github.com/foreni-packages/inviteflood"
packaged-by: [blackarch, kali]
blackarch-builddate: "2025-09-30"
build-system: make
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "foreni-packages/inviteflood"
github-stars: 13
github-pushed: "2015-09-20"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# inviteflood

> UDP SIP INVITE flooder from the Hacking Exposed VoIP toolset — buries SIP proxies and phones in call-setup requests.

## What it's for

inviteflood floods a VoIP target with SIP/SDP INVITE messages sent as crafted UDP packets, testing how a SIP proxy, PBX or phone system handles a flood of call-setup requests. It is part of Mark Collier and Mark O'Brien's Hacking Exposed VoIP toolset (SecureLogix): each INVITE carries SDP modeled on a hard phone, and successive messages vary the CSeq and branch/Via tags so they look like distinct calls rather than one replayed packet. Five arguments are mandatory (interface, target user, target domain, target IP, packet count); the From alias, source address, source/destination ports, SNOM line string, inter-packet delay and verbosity are optional. Use it against your own VoIP infrastructure to find the request rate at which legitimate call setup starts to suffer.

### Flood a SIP proxy with INVITEs

The stock run: one interface, one target user, one packet count.

```
sudo inviteflood eth0 5000 target.example.com 198.51.100.10 10000
```

Outcome: crafts and sends 10,000 INVITE messages for user `5000` at the proxy at 198.51.100.10, each with an incrementing CSeq and fresh transaction tags; on the proxy, SIP transaction tables swell and real registrations or call setups slow down — measure where the target breaks. Requires root for raw packet injection.

### Pace the flood, mask the source and watch each packet

Add control over timing, From identity and source address, with verbose output.

```
sudo inviteflood eth0 5000 target.example.com 198.51.100.10 100000 -a jane.doe -i 198.51.100.20 -S 5061 -s 100 -v
```

Outcome: sends 100,000 INVITEs spaced 100 microseconds apart, with From alias `jane.doe`, spoofed source address 198.51.100.20 and source port 5061 instead of the default discard port 9; verbose mode prints each packet as it goes, so a simultaneous packet capture on the target shows the arriving flood in lockstep with the sender.

*No corpus evidence — derived from upstream documentation.*
