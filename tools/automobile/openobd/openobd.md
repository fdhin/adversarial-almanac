---
name: "openobd"
gentoo-category: "app-misc"
version: "9999"
homepage: "https://sourceforge.net/projects/openobd/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: cmake
meta-checked: "2026-11-09"
github-repo: "milaq/openOBD"
github-stars: 3
github-pushed: "2014-05-25"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# openobd

> Desktop GUI (wxWidgets) for OBD-II vehicle diagnostics through an ELM327 adapter — read and clear MIL/error codes and watch live Mode 1 PID values over a serial link.

## What it's for

A small C++ GUI application (version 0.5.0) that turns a cheap ELM327 adapter connected over a serial port into a diagnostic dashboard. It speaks the OBD-II protocol layer above the adapter: the MIL Status panel lists the stored malfunction-indicator error codes and can reset them, and the PID panel shows the ECU's outputs for every Mode 1 PID the vehicle supports. It logs to SQLite and is cross-platform (wxWidgets), packaged for Gentoo/Pentoo as a live ebuild. It is not a security testing tool and not a CAN bus interface — it works at the standardized OBD-II diagnostic level only, and there is no scriptable CLI; everything happens in the GUI.

## Typical use cases

### Connect to a vehicle through an ELM327 adapter

Read fault codes and live sensor data from a car's OBD-II port.

```
openobd
```

Outcome: the GUI starts; once the ELM327's serial port is selected and connected, the MIL Status panel shows any stored error codes with an option to reset them, and the PID panel streams the ECU's Mode 1 PID outputs (RPM, temperatures, speeds and whatever else the vehicle supports) for monitoring and logging.

*No corpus evidence — derived from upstream documentation.*

GitHub: third-party mirror of the SourceForge upstream (openOBD 0.5.0 tree, identical except two source files patched to build against newer wxWidgets).
