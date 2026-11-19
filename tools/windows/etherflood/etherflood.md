---
name: "etherflood"
categories:
  - blackarch-windows
version: "1.1"
homepage: "http://ntsecurity.nu/toolbox/etherflood/"
packaged-by: [blackarch]
blackarch-builddate: "2016-07-28"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# etherflood

> Floods a switched network with Ethernet frames carrying random hardware addresses to knock switches into flooding mode.

## What it's for

EtherFlood, by Arne Vidstrom (ntsecurity.nu), performs a MAC-table (CAM) overflow against a switched Ethernet network: it sends a stream of Ethernet frames with random hardware addresses so that vulnerable switches run out of forwarding-table entries and start sending all traffic out on every port — at which point a sniffer on the segment sees traffic that was supposed to be isolated. Its practical value is auditing: it tells you whether a switch falls for this classic failure or is engineered to survive it (an immune switch keeps isolating traffic no matter how fast its LEDs blink). It runs from a Windows Command Prompt and requires the bundled EthDrv NDIS protocol driver to be installed first; it takes no command-line options.

## Typical use cases

### Test a switch for MAC-flooding weakness

Install the bundled EthDrv protocol driver (EthDrv.inf) on the Windows host, then launch the tool from a Command Prompt and watch the segment with a sniffer.

```
etherflood.exe
```

Outcome: the host starts emitting frames with randomized source addresses; on a vulnerable switch the port LEDs blink rapidly and other machines' traffic becomes visible to your sniffer, while on an immune switch traffic stays isolated — evidence the switch enforces safeguards against MAC-table overflow.

*No corpus evidence — derived from upstream documentation.*
