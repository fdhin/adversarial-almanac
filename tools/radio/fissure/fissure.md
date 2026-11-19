---
name: "fissure"
categories:
  - radio
homepage: "https://github.com/ainfosec/FISSURE"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: python
license: GPL-3.0
github-repo: "ainfosec/FISSURE"
github-stars: 2081
github-match: "exact"
gh-checked: "2026-11-13"
github-commits-year: 126
gh-activity-checked: "2026-11-13"
worth: 8.0
worth-basis: "full (activity+popularity+recency+adoption)"
---

# fissure

> AIS's open-source RF framework: one PyQt dashboard for SDR signal discovery, analysis, protocol reverse engineering, targeting and geolocation — scaling from a standalone workstation to distributed sensor nodes.

## What it's for

FISSURE (Frequency Independent SDR-based Signal Understanding and Reverse Engineering, by Assured Information Security) is a framework for RF analysis and automation: it stitches SDR hardware, GNU Radio signal processing, IQ capture/replay, protocol analysis, fuzzing and packet-crafting workflows, geolocation and targeting into a single extensible environment. The Dashboard organizes work as connected workspaces sharing Sensor Nodes, SOIs (signals of interest), Targets, Detections, Artifacts and Findings — a Tactical map view for situational awareness, a Signal Analysis workflow that walks from survey/detection through capture, conditioning, classification to protocol discovery, and a Targets & Actions view for running reusable plugin Actions (focused, sequential, fuzzing, packet-crafting). It supports USRP, HackRF, RTL-SDR dongles, LimeSDR, bladeRF, PlutoSDR, SDRplay and 802.11 adapters, runs standalone (Dashboard + local sensor node on one box) or distributed (remote sensor nodes over IP, headless hub, TAK/ATAK integration), and integrates AI/ML and external tools via plugins. It is a heavyweight GUI framework rather than a single CLI: the installer pulls a large dependency tree (GNU Radio 3.8/3.10, PyQt5, drivers), and it is meant for authorized RF testing, research and education on hardware you are licensed to transmit against.

## Typical use cases

### Launch the standalone workstation

Start everything on one box after installation.

```
fissure
```

Outcome: opens the Dashboard GUI as your normal user (never with sudo; launching from a terminal shows useful diagnostic output); a local Sensor Node can then be started from the top controls, giving a single system running Dashboard, HIPRFISR and Sensor Node together.

### Run the headless hub

Operate the coordination half without a GUI.

```
fissure-hiprfisr
```

Outcome: starts the HIPRFISR hub services without the Dashboard — for distributed deployments it accepts connections from remote Sensor Nodes, optionally connects to TAK, and keeps running with no display attached.

### Attach a remote sensor node

Bring a second SDR-equipped machine into the framework.

```
fissure-sensor-node
```

Outcome: after configuring `YAML/Sensor_Node_Config/default.yaml` (unique `nickname`, the hub's `hiprfisr_ip_address`, available `hardware`, optional `autorun` for unattended Action playlists) and exchanging the generated TLS key files, the node connects to the hub, shows up in the Dashboard for tasking, and runs until Ctrl+C.

### Clean up after an unclean exit

Kill leftover processes when the GUI did not close cleanly.

```
sudo pkill python3 && sudo pkill -9 -f fissure
```

Outcome: stops all remaining FISSURE processes; for more surgical control, `sudo ps -aux | grep fissure` lists the PIDs to kill individually.

### Walk an unknown signal through analysis (UI workflow)

1. Dashboard → Signal Analysis → run a survey/detection sweep to surface signals of interest.
2. Select the SOI → capture IQ, inspect and condition it (filtering, decimation).
3. Extract features → classify → protocol discovery, with each step's products kept as Artifacts and Findings on the SOI.

Outcome: an identified and documented signal — modulation/protocol conclusions, captured artifacts and structured findings attached to the SOI, reusable in the Library for packet building and replay.

### Execute plugin Actions against a target (UI workflow)

1. Dashboard → Targets & Actions → register the emitter as a Target.
2. Pick a plugin Action (focused, sequential, fuzzing or packet-crafting workflow).
3. Execute it against the selected Sensor Node/interface and review the resulting Artifacts.

Outcome: the chosen RF interaction (e.g. a protocol fuzzing sequence or crafted packet burst) runs on the selected node's hardware, with outputs captured as artifacts for analysis — capabilities extend without touching the FISSURE core by dropping in new plugins.

No corpus evidence — derived from upstream documentation.
