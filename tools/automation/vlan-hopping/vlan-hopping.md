---
name: "vlan-hopping"
categories:
  - blackarch-automation
version: "21.a37ba4e"
homepage: "https://github.com/nccgroup/vlan-hopping"
packaged-by: [blackarch]
blackarch-builddate: "2019-01-03"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "nccgroup/vlan-hopping"
github-stars: 392
github-pushed: "2016-02-24"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# vlan-hopping

> NCC Group's "Frogger" script — sniffs CDP and 802.1Q traffic, auto-launches a DTP trunk attack, and enumerates live hosts on every discovered VLAN.

## What it's for

vlan-hopping (aka Frogger, by Daniel Compton / NCC Group) is a single bash script (`frogger.sh`) that chains the classic layer-2 VLAN attacks into one run: it sniffs Cisco Discovery Protocol packets to extract the VTP domain, management address, native VLAN and IOS version; it enumerates 802.1Q VLAN IDs from tagged frames inside STP traffic; it automatically attempts a DTP (Dynamic Trunking Protocol) attack to turn the switchport into a trunk; then it arp-scans each discovered VLAN for live hosts and can create a tagged VLAN interface so you stay connected to the network you just hopped into. It depends on arp-scan (≥1.8 for VLAN tag support), yersinia, tshark, screen and vconfig. Run it on a physical connection to the target switchport — VMware/virtual NICs often strip 802.1Q tags and break the technique (the README suggests an external USB NIC as a workaround). Upstream has been dormant since 2016 but the underlying switch misconfigurations (CDP+DTP enabled on access ports) still occur.

## Typical use cases

### Run the full VLAN enumeration and hopping chain

```
sudo ./frogger.sh
```

Outcome: as root on the interface attached to the target switchport, the script sniffs CDP for native-VLAN details, harvests 802.1Q VLAN IDs from STP frames, fires the DTP trunk attack, then arp-scans each discovered VLAN and reports live hosts, offering to create a VLAN interface onto a discovered network. Watch its screen sessions for the per-VLAN results.

*No corpus evidence — derived from upstream documentation.*
