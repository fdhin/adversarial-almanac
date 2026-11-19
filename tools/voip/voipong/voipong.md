---
name: "voipong"
categories:
  - blackarch-voip
version: "2.0"
homepage: "http://www.enderunix.org/voipong/"
packaged-by: [blackarch]
blackarch-builddate: "2020-07-23"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "EnderUNIX/VoIPong"
github-stars: 32
github-pushed: "2016-10-21"
github-archived: false
github-issues: 3
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# voipong

> EnderUNIX's passive VoIP tap by Murat Balaban — detects SIP, H.323 and Cisco Skinny calls on the wire and dumps G.711 conversations to separate WAV files.

## What it's for

Lawful-interception-style monitoring of voice traffic. VoIPong (C, GPLv2, by Murat Balaban of the EnderUNIX team; the enderunix.org project is mirrored in the EnderUNIX GitHub org) is a libpcap-based daemon that watches a network interface, detects Voice over IP calls across SIP, H.323, Cisco's Skinny Client Control Protocol (SCCP) and RTP/RTCP, and for calls encoded with G.711 dumps the actual conversation into per-call WAV files (via sox, whose path is set in the configuration). Call detection runs on a configurable multi-algorithm framework (the `voipongnets` file selects and tunes matching per network), the codec layer is extensible for third-party decoders, and the daemon is administered at runtime through a management console rather than signals. It is purely passive — it records calls, it never injects or manipulates them; for active RTP attacks see [[rtpinsertsound]] or [[vnak]].

## Typical use cases

### Start tapping an interface

```
voipong
```

Outcome: the daemon starts with `/etc/voipong.conf` (edit the `device` and `soxpath` parameters to match the host first) and begins writing a WAV file per detected G.711 conversation; run `voipong -f -d 3` to keep it in the foreground with debug level 3 while validating the setup, `-c` to point at an alternate config file.

### Watch and control live sessions

```
voipctl
```

Outcome: connects to the management socket (`/var/run/voipongmgmt.sock`) and opens the console — `shcall` lists the calls currently being monitored, `shrtcp` shows the RTCP cache, `killcall <id>` stops one recording session, `rusage`/`uptime` report server state, `logrotate` rotates logs and `shutdown` stops the daemon; `help` lists everything.

*No corpus evidence — derived from upstream documentation.*
