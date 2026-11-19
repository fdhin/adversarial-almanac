---
name: "fiked"
categories:
  - blackarch-honeypot
version: "0.0.5"
homepage: "https://www.roe.ch/FakeIKEd"
packaged-by: [blackarch, kali]
blackarch-builddate: "2025-09-30"
build-system: make
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "droe/fiked"
github-stars: 24
github-pushed: "2023-03-20"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fiked

> Fake IKE daemon that impersonates a Cisco VPN gateway in aggressive mode to capture XAUTH usernames and passwords.

## What it's for

fiked (FakeIKEd, by Daniel Roethlisberger, based on vpnc) is a fake IKE daemon for IPsec VPNs that use IKE aggressive mode with pre-shared keys plus XAUTH — the combination common on older Cisco VPN setups and known to leak user credentials. Given the group pre-shared key, fiked completes IKE phase 1 as if it were the real gateway and then collects the XAUTH username and password each client submits in phase 2; as a honeypot it records these for intelligence, while an actual attack additionally requires redirecting clients' IKE traffic to it (for example with ARP poisoning). It supports IKEv1 aggressive mode only — DES/3DES/AES, MD5/SHA1, DH groups 1, 2 and 5; main mode is not supported. Listening on UDP/500 requires root.

## Typical use cases

### Capture XAUTH credentials as a fake gateway

Impersonate the VPN gateway and log every submitted credential.

```
sudo fiked -g 198.51.100.1 -k group1:g3h31m -l account.log
```

Outcome: fiked answers aggressive-mode handshakes for group1 using pre-shared key g3h31m, and each client that completes XAUTH against it appends a grep-friendly line containing the captured username and password to account.log.

### Run unattended as a daemon

Keep the decoy running in the background with separated credential and debug logs.

```
sudo fiked -g 198.51.100.1 -k group1:g3h31m -l account.log -d -L fiked.log -u nobody
```

Outcome: fiked detaches from the terminal, writes verbose protocol logging to fiked.log, drops privileges to nobody after opening its sockets, and keeps appending captured credentials to account.log until stopped.

*No corpus evidence — derived from upstream documentation.*
