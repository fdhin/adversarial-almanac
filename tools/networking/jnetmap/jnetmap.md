---
name: "jnetmap"
categories:
  - blackarch-networking
version: "0.5.5"
homepage: "https://sourceforge.net/projects/jnetmap/files/jNetMap%200.5.5-RC2/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2022-03-07"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
worth: 1.1
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# jnetmap

> Graphical Java network monitoring and documentation tool: draw your network as a map and jNetMap pings every device on a schedule, flags status changes, and can notify or run scripts on up/down events.

## What it's for

jNetMap (by rakudave, hosted at rakudave.ch/jnetmap and on SourceForge) is a desktop "mission control" display for a network you model yourself: you draw devices and connections on a map, and it periodically checks each one (ping, alternative ping method, or a service port), updating its status on the map. It can send e-mail notifications or execute a script when a device goes down or comes back up, attach notes to devices, and launch RDP/VNC/SSH connections to them from the map. It is explicitly not a network discovery or auditing tool — the built-in scanner just helps populate the initial map, and the job is ongoing monitoring and documentation. Ships as a jar that the `jnetmap` wrapper runs under Java.

## Typical use cases

### Launch the network map

Open the GUI to load or build a monitored map.

```
jnetmap
```

Outcome: starts the jNetMap application (the wrapper executes the packaged jar with `java`); from there File > New creates a map, Tools > Network Scanner sweeps your local range and offers the devices it finds for adding, and saved maps reopen for continuous monitoring with status changes and notifications.

*No corpus evidence — derived from upstream documentation.*
