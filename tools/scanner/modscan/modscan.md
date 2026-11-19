---
name: "modscan"
categories:
  - blackarch-scanner
version: "0.1"
homepage: "https://code.google.com/p/modscan/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "moki-ics/modscan"
github-stars: 30
github-pushed: "2015-09-05"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# modscan

> Find Modbus TCP devices across an IP range and report each one's slave (unit) ID.

## What it's for

modscan (Mark Bristow, 2008) sweeps an IP range for Modbus TCP services — PLCs, RTUs and other SCADA kit — and identifies each responding device's slave ID by issuing Modbus function code 17 (Report Slave ID). Output is one `ip:port<TAB>sid` line per device, making it a fast first-pass inventory of an ICS segment. It only speaks the unauthenticated Modbus TCP protocol: it does not read registers, write coils or attempt any control action. It is a Python 2 script with a sequential probe per host, so raise the timeout for slow or distant PLCs.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Sweep a subnet for Modbus devices

Inventory every Modbus TCP endpoint on the standard port.

```
modscan 198.51.100.0/24
```

Outcome: each host answering on port 502 is probed and printed as `ip:port<TAB>slaveid`; the first slave ID found per host is reported and silent hosts are skipped.

### Scan a non-standard Modbus port with a slower timeout

Check control networks that moved Modbus off 502 or have slow devices.

```
modscan -p 5020 -t 1000 198.51.100.0/24
```

Outcome: the sweep probes port 5020 with a 1000 ms per-socket timeout (default 500 ms), catching slower PLC responses on the alternate port.

### Enumerate every slave ID behind a device

Keep querying past the first response to find gateways fronting multiple slaves.

```
modscan -a 198.51.100.10
```

Outcome: instead of stopping at the first match, modscan keeps testing candidate unit IDs on the host and prints every slave ID that answers — the pattern for Modbus TCP-to-serial gateways serving several downstream devices.

A custom probe can be sent with `-f <function code>` and `--data "\x00\x01"` when a device does not answer the default function 17.
