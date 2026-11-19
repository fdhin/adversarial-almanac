---
tool: "rvi-capture"
build-system: "scripts"
upstream: "https://github.com/gh2o/rvi_capture"
recipe: "blackarch"
source: "https://github.com/gh2o/rvi_capture"
license: "custom:unknown"
blackarch-pkgver: "14.a2e129b"
---

# Installing rvi-capture

> Capture packets sent or received by iOS devices.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rvi-capture) (version `14.a2e129b`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S rvi-capture    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/gh2o/rvi_capture.git
cd rvi_capture
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libimobiledevice`
  - `python`
  - `usbmuxd`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[rvi-capture]] · [[BlackArch Tools]]

