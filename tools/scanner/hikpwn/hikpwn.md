---
name: "hikpwn"
categories:
  - blackarch-scanner
version: "8.5a7d69c"
homepage: "https://github.com/4n4nk3/HikPwn"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "4n4nk3/HikPwn"
github-stars: 129
github-pushed: "2021-11-02"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hikpwn

> Discovers Hikvision cameras and NVRs on the local network (passively or via UPnP) and exploits ICSA-17-124-01 to dump user lists and reset admin passwords.

## What it's for

HikPwn (Ananke, Python 3.8) was built from observed Hikvision software and device traffic. It finds devices two ways — passive sniffing on the chosen interface and active UPnP/UDP probing that returns serial numbers, firmware versions and configuration state — then optionally tests each device for ICSA-17-124-01, an information disclosure that reveals valid accounts and permits changing the admin password. Enumerated data is saved to `./log.txt`. It needs root for raw sockets, and as of the packaged version it does not cover CVE-2021-36260.

## Typical use cases

### Passively discover Hikvision devices

```
sudo hikpwn --interface eth0 --address 198.51.100.10
```

Outcome: sniffs on eth0 for about 30 seconds and lists every Hikvision device observed with its IP and MAC address; the enumeration is saved to `./log.txt`.

### Actively enumerate and exploit ICSA-17-124-01

```
sudo hikpwn --interface eth0 --address 198.51.100.10 --active --ICSA_17_124_01
```

Outcome: UPnP probing returns per-device details (model, firmware, activation status), then each discovered device is checked for ICSA-17-124-01 — vulnerable devices expose their user list and an interactive prompt offers to change the admin password.

*No corpus evidence — derived from upstream documentation.*
