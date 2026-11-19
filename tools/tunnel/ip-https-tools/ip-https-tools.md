---
name: "ip-https-tools"
categories:
  - blackarch-tunnel
version: "7.170691f"
homepage: "https://github.com/takeshixx/ip-https-tools"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "takeshixx/ip-https-tools"
github-stars: 9
github-pushed: "2015-08-25"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ip-https-tools

> Detection helpers for Microsoft's IP-HTTPS tunneling protocol (DirectAccess): an Nmap NSE script and a Python probe that tell you whether a host speaks IP-HTTPS on 443.

## What it's for

Reconnaissance of DirectAccess edges. IP-HTTPS is the Microsoft protocol (specification [MS-IPHTTPS]) that carries IPv6 packets inside an HTTPS session so DirectAccess clients keep connectivity from IPv4-only networks; a host answering it is announcing a DirectAccess role. This package ships two detectors by takeshixx (2014-2015, Python 2 era): `ip-https-discover.nse`, which folds the check into an Nmap scan, and `ip-https-test.py`, a standalone probe that opens TLS on 443, sends the protocol's oversized POST (`Content-Length: 18446744073709551615`) and looks for the HTTP 200 reply. They only detect the protocol's presence — they do not establish a tunnel or authenticate to it.

## Typical use cases

### Detect IP-HTTPS during a port scan

```
nmap -p443 --script ip-https-discover.nse 198.51.100.10
```

Outcome: on a supporting host the script reports `IP-HTTPS is supported. This indicates that this host supports Microsoft DirectAccess.` under the 443/tcp line — flagging a DirectAccess edge for follow-up instead of leaving port 443 as generic HTTPS.

### Probe a single host from the command line

BlackArch installs the Python probe as `ip-https-test` (upstream form: `python2 ip-https-test.py HOST`):

```
ip-https-test 198.51.100.10
```

Outcome: prints `IP-HTTPS is supported.` when the bogus POST gets an HTTP/1.1 200, or `Error: IP-HTTPS is not supported.` otherwise — a one-host check without a full scan.

*No corpus evidence — derived from upstream documentation.*
