---
name: "ilo4-toolbox"
categories:
  - blackarch-scanner
version: "47.672a5d6"
homepage: "https://github.com/airbus-seclab/ilo4_toolbox"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: go
license: GPL2
meta-checked: "2026-11-09"
github-repo: "airbus-seclab/ilo4_toolbox"
github-stars: 449
github-pushed: "2024-01-16"
github-archived: true
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ilo4-toolbox

> Airbus CyberSecurity's HPE iLO4/iLO5 research toolbox: a Go network scanner (`iloscan`) that inventories iLO BMCs and their firmware versions, plus the firmware-analysis tooling from their REcon/SSTIC BMC research.

*No corpus evidence — derived from upstream documentation (README.rst of the archived airbus-seclab/ilo4_toolbox repository; scanner behavior read from `scanner/iloscan.go`).*

## What it's for

This toolbox accompanies Airbus' security research into HPE's iLO out-of-band management controllers (REcon Brussels 2018, SSTIC 2018, ZeroNights 2018). Its practical everyday piece is `iloscan`: it sweeps an IP range fetching the unauthenticated `/xmldata?item=ALL` page that iLO serves, and from the returned RIMP XML extracts the server model and iLO firmware version. Knowing the firmware version lets you check a BMC against the research's CVEs — CVE-2017-12542 (authentication bypass and RCE, fixed in iLO4 2.54), CVE-2018-7078 (code execution, fixed in iLO4 2.60/iLO5 1.30) and CVE-2021-29202 (host-to-iLO code execution) — because iLO sits on the motherboard with network, flash and CPU of its own and full control of the host. The rest of the toolbox is firmware-oriented: decryption/extraction scripts for iLO4 and iLO5 firmware images. The upstream repository is archived (2024); the tooling still applies to deployed Gen8/Gen9 hardware.

## Typical use cases

### Inventory iLO BMCs across a subnet

```
iloscan 198.51.100.0/24
```

Outcome: Fetches `/xmldata?item=ALL` on every address and prints the responders with their iLO firmware version and server model (e.g. `{{ RIMP} [{{ HSI} ProLiant DL380 Gen9}] [{{ MP} 2.40 ILOCZJ641057H ...}]}`); the full results are appended to `/tmp/iloscan.log`.

### Check a single known BMC

```
iloscan 198.51.100.10/32
```

Outcome: Same probe for one host (single IPs are expressed as a /32), printing its generation and exact firmware string — compare it against the fixed versions for CVE-2017-12542 (2.54) and CVE-2018-7078 (2.60) to decide exposure.
