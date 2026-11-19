---
name: "bt_audit"
categories:
  - blackarch-bluetooth
version: "0.1.1"
homepage: "http://www.betaversion.net/btdsd/download/"
packaged-by: [blackarch]
blackarch-builddate: "2025-11-30"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "Seabreg/bt_audit"
github-stars: 1
github-pushed: "2019-10-02"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bt_audit

> Bluetooth port scanners: psm_scan sweeps L2CAP PSMs and rfcomm_scan sweeps RFCOMM channels, exposing endpoints that are hidden from SDP.

## What it's for

bt_audit (Collin R. Mulliner) is a small suite for auditing which L2CAP PSMs and RFCOMM channels a Bluetooth device leaves open — including manufacturer backdoors and undocumented services that do not appear in the SDP database. `psm_scan` sweeps the odd-valued PSM range 1-65535 using raw sockets (root required; `-c` switches to a plain connect scan), and `rfcomm_scan` sweeps RFCOMM channels 1-30. Run both against a device and compare the open endpoints with what its SDP records advertise: anything open but unadvertised is the interesting finding. Note that rfcomm_scan's probing connections can trigger pairing if the local host enforces authentication.

## Typical use cases

### Sweep L2CAP PSMs

Find every open PSM on the target.

```
psm_scan -o 00:11:22:33:44:55
```

Outcome: lists the PSMs that accepted a connection (`-o` prints only open ones, without it you get the full probe log); PSMs are odd numbers in 1-65535, `-s`/`-e` bound the range, and `-S` sets the local source BD address.

### Sweep RFCOMM channels

Find every open RFCOMM channel on the target.

```
rfcomm_scan -o -s 1 -e 30 00:11:22:33:44:55
```

Outcome: prints which RFCOMM channels in 1-30 accepted a connection, only the open ones with `-o`; `-d` keeps the ACL link up between probes instead of tearing it down each time.

*No corpus evidence — derived from upstream documentation.*

*GitHub: `Seabreg/bt_audit` is a third-party source mirror of the packaged 0.1.1 tree (README and src/ byte-identical; upstream is Collin Mulliner's betaversion.net release); stat fields predate this link and await recompute.*
