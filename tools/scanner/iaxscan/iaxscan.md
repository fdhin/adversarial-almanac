---
name: "iaxscan"
categories:
  - blackarch-scanner
version: "0.02"
homepage: "http://code.google.com/p/iaxscan/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: mozilla
meta-checked: "2026-11-09"
github-repo: "Seabreg/iaxscan"
github-stars: 0
github-pushed: "2019-03-18"
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

# iaxscan

> Python IAX2 (Asterisk) enumeration toolkit from 2008: discover live IAX2 hosts on a network, then brute-force extension/usernames on them via REGREQ registration probes.

*No corpus evidence — derived from upstream documentation (iaxscan 0.02 as packaged by BlackArch; flags read from the packaged `scan_hosts.py`/`scan_users.py` optparse interfaces).*

## What it's for

iaxscan (nnp, unprotectedhex.com) targets the Inter-Asterisk eXchange protocol 2 used by Asterisk and compatible PBXes for VoIP trunking and phone registration. It works in two stages shipped as two scripts: a host scanner that sweeps an IP range for servers answering on the IAX2 port (default 4569/UDP), and a user scanner that brute-forces usernames against a live host by sending IAX2 registration requests (REGREQ) and distinguishing accepted registrations from rejections. Found usernames are the raw material for VoIP password attacks and toll-fraud assessment. It is Python-2-era code (tested on 2.4/2.5 by its author) and speaks only IAX2 — for SIP-trunk auditing use a SIP-specific tool instead. BlackArch installs the two stages as `iax-scan-hosts` and `iax-scan-users`.

## Typical use cases

### Discover live IAX2 hosts on a subnet

```
iax-scan-hosts -i 198.51.100.0/24
```

Outcome: Walks the range probing UDP port 4569 and prints each host that answers as an IAX2 server — these are the targets for the user-enumeration stage.

### Sweep a non-standard IAX2 port with quieter logging

```
iax-scan-hosts -i 198.51.100.0/24 -p 4570 -v 3
```

Outcome: Same sweep against port 4570 (`-p`, default 4569) with verbosity lowered from the default 5 (`-v`) to reduce output noise on large ranges.

### Brute-force usernames on a discovered PBX

```
iax-scan-users -i 198.51.100.10 -s 1000 -e 2000
```

Outcome: Sends IAX2 registration requests for each username in the 1000-2000 sequence (`-s`/`-e`) against the host and reports which ones the PBX accepts — accepted names are candidates for registration password attacks. Add `-p` if the service is not on port 4569.
