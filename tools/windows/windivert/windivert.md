---
name: "windivert"
categories:
  - blackarch-windows
version: "2.2.0"
homepage: "https://github.com/basil00/WinDivert"
packaged-by: [blackarch]
blackarch-builddate: "2019-12-24"
build-system: windows
license: LGPL3
meta-checked: "2026-11-09"
github-repo: "basil00/WinDivert"
github-stars: 3347
github-pushed: "2023-03-15"
github-archived: false
github-issues: 66
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# windivert

> WinDivert — user-mode Windows packet capture/modify/drop/reinject library with a filter language and example tools.

## What it's for

WinDivert (basil00) is a user-mode packet interception library for Windows 7/8/10: a signed kernel driver (`WinDivert.sys`) plus `WinDivert.dll` expose an API that lets your own program capture, inspect, modify, drop or re-inject network packets at the network layer, selected by a declarative filter language (`"outbound and tcp.DstPort == 80"`) with priority levels. It is the building block other tools embed for user-mode sniffers, firewalls, NATs, tunnels and traffic rewriting — and it ships with small ready-built example binaries (netdump, netfilter, streamdump, socketdump, flowtrack, webfilter, passthru, windivertctl) that are handy directly. It handles loopback and IPv6, and is LGPLv3. It is a library plus examples, not a full analyzer GUI.

## Typical use cases

### Dump packets matching a filter

Use the bundled netdump example as an instant filtered sniffer.

```
netdump.exe "outbound and tcp.DstPort == 80" 1000
```

Outcome: prints a line per matching outbound HTTP packet (the optional second argument is the filter priority); requires the WinDivert driver alongside, and administrator rights to load it.

### Drop traffic matching a rule

Run the netfilter example to silently discard matching packets — a quick outbound block without configuring the Windows firewall.

```
netfilter.exe "outbound and ip.DstAddr == 198.51.100.10"
```

Outcome: packets to the given address are captured and dropped instead of leaving the host; the target becomes unreachable from this machine until you stop the filter process.

*No corpus evidence — derived from upstream documentation (WinDivert README, usage strings in the packaged example sources).*
