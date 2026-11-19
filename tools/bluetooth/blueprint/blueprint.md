---
name: "blueprint"
categories:
  - blackarch-bluetooth
version: "0.1_3"
homepage: "https://trifinite.org/trifinite_stuff_blueprinting.html"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# blueprint

> Fingerprint a Bluetooth device from its SDP service record and identify the handset model against a signature database — nmap-style recognition for Bluetooth.

*No corpus evidence — derived from upstream documentation (the packaged upstream source archive, BluePrint v0.1 by the trifinite group, is the reference).*

## What it's for

BluePrint (Collin Mulliner and Martin Herfurt, trifinite group, 2004) identifies what a Bluetooth device actually is without cooperation from the device itself. It reads the output of `sdptool browse` — the target's Service Discovery Protocol record — and computes a fingerprint from the service record handles and their channel numbers. That fingerprint is looked up in `blueprint.db`, a database built from real captured devices (the archive ships ~50 signatures for Nokia, Sony Ericsson, Siemens, HP and other era hardware), yielding a vendor/model identification even when the friendly name lies. It is a passive identification aid for audits of legacy Bluetooth fleets, not an attack tool: it neither connects to arbitrary services nor exploits anything. The same fingerprint material (SDP dumps, plus `hcitool info` and OBEX devinfo for richer signatures) is how new devices get added to the database.

## Typical use cases

### Identify a device by its SDP fingerprint

```
sdptool browse --tree --l2cap AA:BB:CC:DD:EE:FF | bp.pl AA:BB:CC:DD:EE:FF
```

Outcome: bp.pl consumes the SDP browse output on stdin, computes the fingerprint hash for the target and looks it up in `blueprint.db` (matching first on the vendor OUI prefix of the address, then on the hash); a known device prints its identification, an unknown one prints the bare fingerprint. `-nomac` widens the lookup by ignoring the OUI entirely.

### Compute a raw fingerprint for a new signature

```
sdptool browse --tree --l2cap AA:BB:CC:DD:EE:FF | bp.pl AA:BB:CC:DD:EE:FF -mkdb
```

Outcome: prints the computed fingerprint hash with no database lookup — the value to record when capturing a new device for the signature collection.

### Rebuild the signature database

```
./mkdb.pl
```

Outcome: regenerates `blueprint.db` from every file in the `devices/` directory (each a saved SDP dump with device info enclosed in `---info`/`/---info` and SDP data in `---sdp`/`/---sdp` tags), making newly contributed signatures available to future lookups.
