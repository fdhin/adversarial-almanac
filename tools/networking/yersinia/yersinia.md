---
name: "yersinia"
categories:
  - blackarch-networking
version: "0.8.2"
homepage: "https://github.com/tomac/yersinia"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2024-12-21"
build-system: autotools
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "tomac/yersinia"
github-stars: 877
github-pushed: "2023-09-15"
github-archived: false
github-issues: 29
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# yersinia

> The classic Layer-2 attack framework: craft and launch attacks against STP, CDP, DHCP, DTP, 802.1Q, VTP, HSRP and ISL from one tool.

## What it's for

Yersinia is a framework for testing weaknesses in layer-2 protocols. It implements attack primitives for Spanning Tree (and RSTP), Cisco Discovery Protocol, Hot Standby Router Protocol, DHCP, Dynamic Trunking Protocol, IEEE 802.1Q, VLAN Trunking Protocol and Inter-Switch Link: rogue-root and TCN-flood BPDUs, rogue DHCP servers, forced trunk negotiation, VTP floods and more, each with editable protocol fields. Three interfaces cover the same functionality — a GTK GUI (`-G`), an interactive ncurses console (`-I`), and one-shot command-line attacks — plus a daemon mode (`-D`) that opens a network listener emulating a Cisco CLI for remote administration. These are disruptive primitives (root-election storms, CAM-table pressure, MITM via DHCP/trunking) meant for auditing switched networks you are authorized to test; against an unhardened switch most attacks are denial of service or traffic-interception conditions.

## Typical use cases

### Explore attacks in the interactive ncurses console

Browse each protocol's attacks with editable fields before launching.

```
yersinia -I
```

Outcome: Opens the interactive console listing the supported protocols; selecting one (e.g. STP) shows its attacks with every protocol field editable in place, so a launch can be tuned to the target network before firing.

### Claim the Spanning Tree root role

Insert the testing host into the tree as root (non-DoS MITM position).

```
yersinia stp -attack 4
```

Outcome: The non-DoS root-claiming attack: yersinia sniffs the first BPDU on the first non-loopback interface, reuses its field values with a lower bridge priority, and keeps announcing itself until switches elect the local host as the spanning-tree root — putting it on the forwarding path for the segment.

### Flood topology-change BPDUs

Force the whole tree to continuously recompute (DoS).

```
yersinia stp -attack 3 -source 66:66:66:66:66:66 -interface eth0
```

Outcome: Floods topology-change BPDUs from the given source MAC on eth0; the root switch acknowledges each change and floods notifications, and every switch in the tree keeps re-running its STP engine.

### Use the GTK GUI instead of the console

Drive the same attacks from a point-and-click interface.

```
yersinia -G
```

Outcome: Opens the full GTK application where protocols, attacks and parameters are selected from menus and dialogs — functionally equivalent to the ncurses console for operators who prefer a GUI.

### Attack the DHCP service

Exhaust the server's address pool, or take over address assignment via the console.

```
yersinia dhcp -attack 1
```

Outcome: floods the segment with DHCPDISCOVER packets until the real server's address pool is exhausted (continuous DoS). The other DHCP attacks — rogue DHCP server (`-attack 2`) and mass DHCPRELEASE (`-attack 3`) — carry editable parameters (server IP, lease range, router/DNS), so the one-shot CLI refuses them with "doesn't support attacks that needs parameters"; launch those from the interactive console (`-I`) or GUI (`-G`), where the fields can be filled in before firing.

*Command patterns sourced from HackTricks pages (CC BY-NC reference; rewritten), every flag and attack number verified against the yersinia(8) man page and current source.*
