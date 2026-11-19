---
name: "udsim"
categories:
  - blackarch-scanner
version: "33.b379464"
homepage: "https://github.com/zombieCraig/UDSim/"
packaged-by: [blackarch]
blackarch-builddate: "2022-12-24"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "zombieCraig/UDSim"
github-stars: 340
github-pushed: "2022-12-10"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# udsim

> Graphical UDS ECU simulator and fuzzer that learns real vehicle modules from CAN traffic.

## What it's for

UDSim emulates vehicle ECUs that speak UDS (Unified Diagnostic Services) over a CAN interface, so diagnostic and dealership software can be exercised without a car attached. It learns which modules exist and how they answer by passively watching live CAN traffic or candump logs — it speaks ISO-TP and needs no per-vehicle knowledge — then replays the learned behavior in simulation mode. Each module card offers Fake Responses (answer requests it never saw) and a response fuzz level, and learned data can be exported for Peach Fuzzer for heavier fuzzing. SDL2 GUI by Craig Smith (Open Garages), designed to run alongside ICSim; a complete rewrite of the earlier uds-server.

## Typical use cases

### Learn modules from a live CAN bus

```bash
udsim vcan0
```

Outcome: the GUI opens and starts listing UDS-capable modules discovered on the interface; press the disk icon to persist what was learned to `config_data.cfg`, an INI-style file with one section per arbitration ID.

### Replay a captured session in simulation mode

```bash
udsim -l candump.log vcan0
```

Outcome: parses the can-utils candump log, builds the module database from it and automatically switches to simulation mode — no live vehicle or diagnostics session needed.

### Simulate a previously learned vehicle

```bash
udsim -c config_data.cfg vcan0
```

Outcome: UDSim answers bus requests exactly as the learned ECUs did, so diagnostic tools interact with a virtual car; per-module options toggle Fake Responses and the fuzz level for testing those tools' robustness.

*No corpus evidence — derived from upstream documentation (zombieCraig/UDSim README; usage `udsim [options] <can-if>` with -c/-l/-f/-v).*
