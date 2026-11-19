---
name: "cantoolz"
categories:
  - blackarch-automobile
version: "425.82d330b"
homepage: "https://github.com/CANToolz/CANToolz"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "CANToolz/CANToolz"
github-stars: 305
github-pushed: "2023-05-07"
github-archived: false
github-issues: 21
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cantoolz

> Module-driven framework for black-box CAN network analysis — chainable pipe modules (sniff, fuzz, firewall, replay, discovery) driven from a web interface, with a built-in virtual car for practice.

## What it's for

Unified CAN bus analysis for security researchers and automotive OEM testers, a.k.a. YACHT (Yet Another Car Hacking Tool). Instead of many separate one-off car-hacking scripts, it exposes single-purpose modules that are chained in `pipe` configurations: I/O modules for several hardware adapters (USBtin, CANBus Triple, Seeed CAN-BUS Shield, Linux CANSocket, TCP2CAN tunnelling) feed analysis modules such as `ping` (ECU/service discovery with ISO-TP and UDS support), `analyze` (statistics with CSV output, UDS/ISO-TP detection), `fuzz`, `firewall` (block by ID), `replay` and `mod_printMessage`. Configurations are Python files loaded at startup, and the tool serves a browser UI. It also ships VIRCar, a virtual two-bus car (OBD2 + CABIN) so the whole workflow can be exercised without any hardware. Development is effectively dormant (Python 3, early-alpha quality upstream); it does not provide a GUI beyond the web pages and does not cover CAN FD.

## Typical use cases

### Start a sniffing session in the web interface

Load a ready-made analysis configuration and operate it from the browser.

```
cantoolz -g w -c examples/can_sniff.py
```

Outcome: the web GUI is served on `http://localhost:4444` with the sniffing pipe from the example configuration active; captured traffic appears in the browser and can be examined or passed to further modules.

### Bring up the virtual car

Simulate a target vehicle without hardware.

```
cantoolz -g w -p 5555 -c examples/car_config.py
```

Outcome: a second instance serves VIRCar on `http://localhost:5555` — press START on its index page, then open `vircar.html` to see the simulated car's OBD2 and CABIN buses exchanging frames.

### Attack the virtual car

Run the hacker-side configuration against the simulated vehicle.

```
cantoolz -g w -c examples/car_hacker.py
```

Outcome: the web interface on `http://localhost:4444` connects to the running VIRCar over TCP2CAN on both virtual buses, ready for discovery, MITM/firewall and replay experiments against live simulated traffic.

*No corpus evidence — derived from upstream documentation.*
