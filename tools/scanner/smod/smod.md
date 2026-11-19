---
name: "smod"
categories:
  - blackarch-scanner
version: "53.7eb8423"
homepage: "https://github.com/enddo/smod"
packaged-by: [blackarch]
blackarch-builddate: "2018-04-06"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "enddo/smod"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# smod

> Metasploit-style console framework for penetration testing Modbus/TCP — module-based scanners, function fuzzers and DoS attacks aimed at SCADA devices.

## What it's for

SMOD (Farzin Enddo) is a modular framework for testing the Modbus/TCP protocol, written in Python 2 on top of Scapy and driven from a Metasploit-like console (`use`, `set`, `show`, `exploit`). Its ~23 modules cover Modbus service discovery, function-code enumeration, unit-ID (UID) brute forcing, fuzzing of read/write function codes, denial-of-service via coil/register writes, and ARP poisoning with sniffing. It is for vulnerability assessment of Modbus/TCP services (typically port 502) on PLCs and other SCADA gear; it does not implement other ICS protocols. The upstream repository is gone — this reference is derived from an archived copy of the project README; it is also a Python 2 era tool.

## Typical use cases

### List the module tree

Start the console and enumerate what the framework can do.

```
smod
show modules
```

Outcome: opens the SMOD console (version banner, module count, `SMOD >` prompt) and lists every module grouped under `modbus/dos`, `modbus/function`, `modbus/scanner` and `modbus/sniff` — e.g. `modbus/scanner/discover`, `modbus/scanner/getfunc`, `modbus/scanner/uid`.

### Select and run an attack module

Pick a module by name, configure it, fire it.

```
use modbus/scanner/uid
exploit
```

Outcome: the console switches into the module context; module options (the target Modbus service) are set with `set`, and `exploit` runs the module — for `modbus/scanner/uid`, a brute force over Modbus unit IDs, with progress printed to the console. `back` returns to the root context and `exit` quits.

*No corpus evidence — derived from upstream documentation.*
