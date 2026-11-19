---
name: "ikeprobe"
categories:
  - blackarch-windows
version: "0.1"
homepage: "http://www.ernw.de/download/ikeprobe.zip"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: windows
license: custom
meta-checked: "2026-11-09"
github-repo: "Seabreg/ikeprobe"
github-stars: 0
github-pushed: "2019-03-24"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ikeprobe

> Windows auditor that probes an IKE (IPsec) VPN gateway for weak pre-shared-key implementations.

## What it's for

IKEProbe (v0.1beta, 2003, by Michael Thumann of ERNW) checks whether a VPN server's IKE service exposes its pre-shared key to offline attack. Given a peer address it connects to the peer's IKE service (UDP 500), walks the proposals the responder offers, and determines whether aggressive mode with PSK authentication is accepted — the configuration that leaks a keyed hash an attacker can crack offline. It diagnoses the exposure; it does not crack the key itself. It is a Windows console tool from 2003 (bundled with the OpenSSL library `libeay32.dll`), written against the IKE implementations of that era.

## Typical use cases

### Audit a VPN gateway for weak PSK mode

Point ikeprobe at an IKE peer to see how its PSK implementation behaves.

```
ikeprobe.exe 198.51.100.10
```

Outcome: ikeprobe contacts the peer's IKE service, cycles through its proposals, and prints the responder's accepted transforms and authentication modes; an aggressive-mode PSK response means the pre-shared key is recoverable by offline cracking. The peer may also be given as a hostname (`ikeprobe.exe vpn.example.com`).

GitHub: third-party mirror of the ERNW distribution (Seabreg/ikeprobe).

*No corpus evidence — derived from upstream documentation.*
