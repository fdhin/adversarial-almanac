---
tool: "gsmevil2"
build-system: "scripts"
upstream: "https://github.com/ninjhacks/gsmevil2"
recipe: "blackarch"
source: "https://github.com/ninjhacks/gsmevil2"
license: "custom:unknown"
blackarch-pkgver: "18.ce64fa5"
---

# Installing gsmevil2

> Python web-based tool which use for capturing imsi numbers and sms.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gsmevil2) (version `18.ce64fa5`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S gsmevil2    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ninjhacks/gsmevil2) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `appdirs`, `flask`, `flask-socketio`, `pyshark`, `termcolor`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `gr-gsm`
  - `kalibrate-rtl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[radio]] · [[gsmevil2]] · [[BlackArch Tools]]

