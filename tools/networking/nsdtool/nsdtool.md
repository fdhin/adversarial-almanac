---
name: "nsdtool"
categories:
  - blackarch-networking
version: "0.1"
homepage: "http://www.curesec.com/en/publications/tools.html"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/nsdtool"
github-stars: 0
github-pushed: "2019-09-09"
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

# nsdtool

> Netgear switch discovery over NSDP, with unauthenticated password brute-force, password-set and reboot extras.

## What it's for

nsdtool (by Stephan Zeisberg, CureSec GmbH) is a small Python toolkit for discovering and testing Netgear switches on a local network using NSDP, Netgear's proprietary Switch Discovery Protocol. Beyond discovery it exercises the management operations NSDP exposes: brute-forcing the switch password from a wordlist, setting a new management password on a device, and rebooting it. Configure the interface, ports and broadcast delay in `config.ini` before use. It speaks only NSDP over UDP — it is not a general switch scanner, and the password/reboot operations apply only to devices whose firmware still accepts them.

## Typical use cases

### Discover Netgear switches on a subnet

Run the sniffer in one shell and the discovery sweep in another.

```
./main.py -s
```

```
./main.py -d -t 198.51.100.0/24
```

Outcome: the discovery shell (`-d -t <cidr>`) sends NSDP probes across the subnet; every responding device appears in the sniffer shell (`-s`) decoded — device model, MAC, firmware version, default gateway, switch IP and subnet mask.

### Brute-force a switch management password

Try a wordlist against a specific device.

```
./main.py -m aa:bb:cc:dd:ee:01 -bf passwords.txt -np NewPassword1
```

Outcome: each candidate from `passwords.txt` is tried against the switch identified by MAC `aa:bb:cc:dd:ee:01`; the `-np` value doubles as the new password written on a successful attempt, so keep it to retain access.

### Set a new password on a switch

Change the management password with the current one.

```
./main.py -m aa:bb:cc:dd:ee:01 -sp -cp OldPassword1 -np NewPassword1
```

Outcome: with the current password supplied via `-cp`, the switch's management password is set to `NewPassword1` (`-sp` selects set-password mode) — effectively taking over the device's web/management login.

### Reboot a switch

Trigger a restart on a known device.

```
./main.py -m aa:bb:cc:dd:ee:01 -r -p OldPassword1
```

Outcome: the NSDP reboot command is sent; the switch restarts, which clears sessions and can be used to test crash-recovery behavior.

*No corpus evidence — derived from upstream documentation.*
