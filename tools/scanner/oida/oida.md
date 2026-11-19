---
name: "oida"
categories:
  - scanner
homepage: "https://github.com/f0rw4rd/oida"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: python
license: AGPL3
meta-checked: "2026-11-13"
github-repo: "f0rw4rd/oida"
github-match: "exact"
github-stars: 46
gh-checked: "2026-11-13"
github-commits-year: 739
gh-activity-checked: "2026-11-13"
worth: 7.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# oida

> NetExec-style CLI for OT/ICS — one grammar, `oida <protocol> <target>`, to scan, fuzz and assess Modbus, OPC UA, S7, DNP3, BACnet and a dozen more industrial protocols.

## What it's for

OIDA (PyPI package `oida-ics`) is a single-command toolkit for operational-technology security assessment on industrial, energy, building-automation and healthcare networks. Every protocol uses the same `oida <protocol> <target>` syntax — if you know `nxc smb`, you already know `oida modbus` — and scanners ship for Modbus, OPC UA, Siemens S7, DNP3, IEC 60870-5-104, BACnet, EtherNet/IP, PROFINET, HL7, DICOM, MQTT, SNMP and more. Defaults are read-only: any write or state change requires an explicit `--confirm`, a deliberate safety gate for environments where a scan can move physical actuators. Take that seriously — upstream states plainly that the tool can crash devices, trigger actuator writes and extract credentials, and is built for authorized research on systems you own or are contracted to test.

## Typical use cases

### Scan a single OT host

First contact with a PLC or RTU.

```
oida modbus 198.51.100.10
```

Outcome: read-only enumeration of the Modbus service on the default port — the per-protocol output shape is documented at getoida.dev/protocols; append a port to the target (`198.51.100.10:5020`) to hit non-default ports, which wins over any `-p/--port` flag.

### Sweep a subnet

Map a process network.

```
oida modbus 198.51.100.0/24 -t 20
```

Outcome: scans every host in the subnet with `-t` controlling parallelism; any target form may carry a port (`198.51.100.0/24:5020`, `[2001:db8::1]:5020`), and a target file with one `host[:port]` per line is the way to scan mixed-port fleets in one run.

### Assess an OPC UA endpoint

The other classic OT front door.

```
oida opcua opc.tcp://198.51.100.10:4840
```

Outcome: read-only assessment of the OPC UA server at the given endpoint URL, using the same report structure as the other protocol modules.

### Discover ICS devices on the wire

Find what actually runs before scanning.

```
oida discovery eth0
```

Outcome: passive/wire-level discovery of ICS devices reachable from the given interface — feed its results into the per-protocol scanners as targets.

### Read a protocol's full flag set (and the safety gate)

Before touching anything destructive.

```
oida modbus -h
```

Outcome: prints the complete flag set for the Modbus module — including the fuzz/write options that stay dormant unless `--confirm` is passed explicitly on the command line.

No corpus evidence — derived from upstream documentation.
