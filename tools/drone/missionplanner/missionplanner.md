---
name: "missionplanner"
categories:
  - blackarch-drone
version: "1.3.83"
homepage: "https://firmware.ardupilot.org/Tools/MissionPlanner/"
packaged-by: [blackarch]
blackarch-builddate: "2025-09-14"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "ArduPilot/MissionPlanner"
github-stars: 2420
github-pushed: "2026-11-05"
github-archived: false
github-issues: 1370
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 215
gh-activity-checked: "2026-11-09"
---

# missionplanner

> ArduPilot's ground control station: plan waypoint missions on a map, flash and configure autopilots, and analyse flight logs — packaged as the upstream Windows build launched through Mono.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Mission Planner is the reference ground control station for ArduPilot vehicles (Copter, Plane, Rover, Sub): it plans waypoint missions on a map, uploads them to the autopilot, installs/updates flight firmware, tunes the several-hundred-parameter set, streams live telemetry over serial/UDP/TCP links (including SITL instances), and analyses DataFlash and binary logs. It is a GUI-first Windows .NET application; on this distribution it is honestly just the upstream Windows build executed under Mono through the `missionplanner` launcher — upstream's own caveat applies: not all functions are available on Linux. Per-user data (map cache, elevation tiles, parameter definitions) lives under `~/.local/share/Mission Planner/`. It is not an attack tool and has no meaningful headless CLI; everything beyond launching it happens in the GUI.

## Typical use cases

### Start the ground station

Open the GUI and connect to an autopilot or simulator.

```
missionplanner
```

Outcome: the Mission Planner window opens (Mono rendering the packaged `MissionPlanner.exe`); pick a serial port or a UDP/TCP endpoint from the connection dropdown at the top right to attach to a flight controller or a SITL instance, then use the Flight Plan tab to build and upload waypoint missions or the Config tab for firmware and parameters.

### Diagnose a failure to start under Mono

Get verbose runtime diagnostics when the packaged GUI will not come up.

```
MONO_LOG_LEVEL=debug missionplanner
```

Outcome: Mono logs assembly loading, unhandled exceptions and runtime diagnostics to the terminal as the application starts — the standard way upstream documents for debugging startup of the Linux/Mono build; the trace usually names the missing assembly or font/GDI+ failure blocking the GUI.
