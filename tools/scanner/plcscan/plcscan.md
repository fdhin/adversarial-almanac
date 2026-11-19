---
name: "plcscan"
categories:
  - blackarch-scanner
version: "0.1"
homepage: "http://packetstormsecurity.com/files/119726/PLC-Device-Scanner.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "meeas/plcscan"
github-stars: 116
github-pushed: "2023-04-07"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# plcscan

> Identifies industrial PLC devices speaking s7comm (Siemens) or Modbus/TCP.

## What it's for

plcscan (a community continuation of Dmitry Efanov's Positive Research PLC Device Scanner) connects to TCP/102 (Siemens S7comm) and TCP/502 (Modbus) endpoints and reports what the device is. For S7 it reads the module catalogue — module type and order number, hardware and firmware versions, PLC name, plant identification, serial number; for Modbus it probes unit IDs for device identification and error behaviour (e.g. ILLEGAL FUNCTION, GATEWAY TARGET DEVICE FAILED TO RESPOND). It is a Python 2 script exposed as the `plcscan` console. It only identifies and fingerprints — it does not read or write control registers — which makes it a quiet first-touch tool for ICS asset inventory.

## Typical use cases

### Fingerprint a single PLC

Ask one device what it is.

```
plcscan 198.51.100.10
```

Outcome: probes the standard S7 and Modbus ports; a Siemens answer prints the s7comm module table (module, hardware/firmware versions, PLC name, serial number), a Modbus answer prints per-unit-ID device info or error codes.

### Sweep a control network with a raised timeout

Inventory a SCADA segment, tolerating slow PLCs.

```
plcscan --timeout 2 198.51.100.10:102 198.51.100.0/24
```

Outcome: scans the listed targets (mix of host, host:port and CIDR), waiting up to two seconds per probe — industrial devices often need more than the default — and prints one identification block per responding PLC.

### Scan from a host list

Drive the sweep from a prepared file.

```
plcscan --hosts-list hosts.txt
```

Outcome: reads one target per line — bare IP (`198.51.100.15`), IP:port (`198.51.100.107:102`) or hostname (`plc.example.com:502`) — and fingerprints each in turn.

No corpus evidence — derived from upstream documentation (meeas/plcscan README usage examples, matching the packaged Python 2 script).
