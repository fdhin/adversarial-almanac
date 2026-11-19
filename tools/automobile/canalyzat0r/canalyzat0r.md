---
name: "canalyzat0r"
categories:
  - blackarch-automobile
version: "41.6bc251e"
homepage: "https://github.com/schutzwerk/CANalyzat0r"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-04"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "schutzwerk/CANalyzat0r"
github-stars: 795
github-pushed: "2022-02-21"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# canalyzat0r

> GUI security analysis toolkit for proprietary car protocols — graphical sniffing, fuzzing and packet correlation on SocketCAN interfaces, with project-based evidence management.

## What it's for

Black-box analysis of CAN networks from a single Qt window instead of juggling several command-line tools. It grew out of a SCHUTZWERK thesis project and bundles interface configuration (auto-loading kernel modules, managing physical and virtual SocketCAN devices), multi-interface graphical sniffing, sending and fuzzing (basic UDS fuzzing included), dump comparison, "known packets" recognition, background-noise filtering to find action-specific packets, and binary packet filtering with randomization. Work is organized in projects persisted to SQLite and exportable as JSON. It hides processes from nothing — it is an analysis workbench, not an attack tool, and it needs root because it configures CAN interfaces directly. Any SocketCAN-compatible adapter works (tested with USB2CAN and PCAN-USB Pro FD); CAN FD is supported.

## Typical use cases

### Run the analysis GUI against local CAN interfaces

Start the workbench with the privileges it needs to configure interfaces.

```
sudo -E ./CANalyzat0r.sh
```

Outcome: the Qt GUI opens after the wrapper sets up its Python environment; `-E` preserves `QT_X11_NO_MITSHM=1`, which empty-GUI troubleshooting identifies as required. From the interface tab, physical or virtual SocketCAN devices can be brought up, then sniffing, sending and fuzzing threads are added per tab — all actions are logged per project.

### Run the containerized version with host CAN access

Use the upstream Docker image instead of a local Python setup.

```
docker run -it --name canalyzat0r -e DISPLAY=$DISPLAY --net=host --privileged --cap-add=CAP_SYS_MODULE --device /dev/snd -v /tmp/.X11-unix:/tmp/.X11-unix:ro -v /lib/modules:/lib/modules -v /path/to/CANalyzat0r/files/database.db:/opt/CANalyzat0r/data/database.db -v /path/to/sharedFolder:/root/sharedFolder schutzwerk/canalyzat0r:latest
```

Outcome: the GUI appears on the local X display while the container holds host CAN access (`--net=host`, `--privileged`, module loading via `/lib/modules`); projects persist across runs through the mounted `database.db`, and files exchange through `sharedFolder`. Requires a prior `xhost +local:root`.

*No corpus evidence — derived from upstream documentation.*
