---
tool: "fissure"
build-system: "python"
upstream: "https://github.com/ainfosec/FISSURE"
recipe: "upstream"
license: "GPL-3.0"
---

# Installing FISSURE

> RF/SDR analysis framework (Dashboard, HIPRFISR, Sensor Node) — PyQt5 + GNU Radio, by Assured Information Security.

Source: hacktricks feed; upstream [`ainfosec/FISSURE`](https://github.com/ainfosec/FISSURE).

## Installer (Ubuntu/Kali-class systems)

The current codebase lives on the `Python3` branch (PyQt5, GNU Radio 3.8 or 3.10 depending on OS); releases are periodic snapshots, so prefer the branch.

```bash
git clone https://github.com/ainfosec/FISSURE.git
cd FISSURE
git checkout Python3
./install
```

The installer detects the OS, prompts for PyQt dependencies and optional third-party software, and offers modes: `Full` (broadest), `Base` (standalone workstation), `Dashboard`, `HIPRFISR` (headless hub), `Sensor Node` (remote node), or `Custom`. Items with a Verify step are checked post-install.

## Rules and caveats (from upstream)

- Install and run from a user-owned directory (e.g. home) — do NOT run `./install` or `fissure` with `sudo`.
- Cleanest on a clean OS install; conflicts with existing packages are the main failure mode.
- Best-tested on Ubuntu (18.04/20.04/22.04/24.04), Kali, DragonOS and Raspberry Pi OS; Parrot/BackBox/KDE neon are in-progress. Windows 11 works via WSL2 (see README).
- Reboot (or re-login) after install so udev group/device-permission changes take effect.
- Third-party software lands in `~/Installed_by_FISSURE`; GNU Radio flowgraphs may need recompiling across GR versions.

## Containerized

Apptainer definition files build repeatable `Dashboard` / `HIPRFISR` / `Sensor Node` / `Base` / `Full` / custom environments (primary tested config: Ubuntu 24.04 host and container).

## Launch

```bash
fissure                  # Dashboard GUI (normal user, from a terminal)
fissure-hiprfisr         # headless hub
fissure-sensor-node      # remote sensor node
```

Backlinks: [[radio]] · [[fissure]]
