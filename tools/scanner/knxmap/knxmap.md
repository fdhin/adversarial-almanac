---
name: "knxmap"
categories:
  - blackarch-scanner
version: "252.6f40dd1"
homepage: "https://github.com/ernw/knxmap"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "ernw/knxmap"
github-stars: 122
github-pushed: "2024-03-15"
github-archived: true
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# knxmap

> KNXmap: scanner and auditor for KNXnet/IP gateways and the KNX bus behind them.

## What it's for

KNXmap (ERNW) is a Python/asyncio tool for scanning and auditing KNXnet/IP gateways —
the bridges between IP networks and KNX building-automation buses (EN 50090). It
discovers gateways via multicast search requests, scans gateways and the bus devices
behind them across individual-address ranges, reads device descriptions and
configurations, and goes beyond read-only auditing: it can monitor live bus or group
telegrams, write arbitrary values to group addresses (lights, shutters, HVAC
setpoints), execute low-level APCI device functions and bruteforce a device's
authorization key. Default target port is UDP 3671; bus scanning tunnels into the
gateway. Everything it does is visible on a physical bus — actuating real loads —
so it belongs on authorized engagements on isolated lab installations. The upstream
repository is archived (2024) and documentation lives in its GitHub wiki.

## Typical use cases

### Discover KNXnet/IP gateways on the local network

```
knxmap search
```

Outcome: Sends SEARCH_REQUEST multicasts (default 224.0.23.12) and lists every KNXnet/IP gateway that answers, with its control/tunnel endpoints — the starting point for every other subcommand.

### Scan a gateway and enumerate bus devices

```
knxmap scan 198.51.100.10 1.1.0-1.1.10 --bus-info
```

Outcome: Tunnels into the gateway and probes the given individual-address range; alive devices are listed with their descriptions, and `--bus-info` additionally extracts per-device information (serial, firmware, config), giving a full inventory of the installation.

### Monitor live bus or group traffic

```
knxmap monitor 198.51.100.10 --group-monitor
```

Outcome: Opens a tunnel and prints telegrams in real time — group-address traffic with `--group-monitor`, raw bus-level messages without — so you can observe which group addresses actuate which functions before writing anything.

### Write a value to a group address

```
knxmap write 198.51.100.10 1/0/5 1
```

Outcome: Sends a group write of value `1` to group address `1/0/5` through the gateway (add `--routing` to use KNXnet/IP Routing instead of Tunnelling), toggling whatever load is bound to that address — the demonstration that the bus is attacker-controllable.

*No corpus evidence — derived from upstream documentation and the packaged argparse definitions.*
