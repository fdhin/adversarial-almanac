---
tool: "hdmi-sniff"
build-system: "scripts"
upstream: "https://github.com/ApertureLabsLtd/hdmi-sniff"
recipe: "blackarch"
source: "https://github.com/ApertureLabsLtd/hdmi-sniff"
license: "custom:unknown"
blackarch-pkgver: "5.f7fbc0e"
---

# Installing hdmi-sniff

> HDMI DDC (I2C) inspection tool. It is designed to demonstrate just how easy it is to recover HDCP crypto keys from HDMI devices.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hdmi-sniff) (version `5.f7fbc0e`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S hdmi-sniff    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ApertureLabsLtd/hdmi-sniff) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `hdcp-genkey`
  - `python2`
- ⚠️ Python 2 only deps: python2-pyserial (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[hardware]] · [[hdmi-sniff]] · [[BlackArch Tools]]

