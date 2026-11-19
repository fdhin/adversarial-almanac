---
name: "mfsniffer"
categories:
  - blackarch-sniffer
version: "0.1"
homepage: "http://packetstormsecurity.com/files/120802/MF-Sniffer-TN3270-Password-Grabber.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "mainframed/MFSniffer"
github-stars: 52
github-pushed: "2023-06-06"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mfsniffer

> Captures cleartext TN3270 mainframe logins — TSO user IDs and passwords — off the wire.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Watching a network segment for unencrypted TN3270 sessions to a specific mainframe and extracting the TSO logon credentials from the terminal stream. You give it the mainframe's IP, its TN3270 listening port and the interface to sniff on; scapy does the capture and the script prints user IDs and passwords as users log on. It was tested by the author (Soldier of Fortran, @mainframed767) against x3270 and TN3270X clients and does nothing against SSL-wrapped sessions — TN3270 over TLS, typically port 923, stays opaque. Its niche is auditing mainframe estates where legacy cleartext 3270 access is still permitted. Python 2 based.

## Typical use cases

### Watch a mainframe's standard TN3270 port

```
sudo mfsniffer -a 198.51.100.10 -p 23 -i eth0
```

Outcome: scapy captures on eth0, filtered to traffic with the mainframe at 198.51.100.10 port 23; when a user logs on to TSO over cleartext TN3270, the sniffed user ID and password are printed. The packaged command `mfsniffer` runs the upstream `MFSniffer.py`.

### Cover a nonstandard TN3270 port

```
sudo mfsniffer -a 198.51.100.10 -p 623 -i eth0
```

Outcome: same capture for deployments that moved TN3270 off port 23 (2323, 623 and similar); logins on that port are decoded, while any SSL-encrypted sessions (e.g. on 923) yield nothing.
