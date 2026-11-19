---
tool: "limeaide"
build-system: "scripts"
upstream: "https://github.com/kd8bny/LiMEaide"
recipe: "blackarch"
source: "https://github.com/kd8bny/LiMEaide"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v2.0.0.r3.gce3c9b7"
---

# Installing limeaide

> Remotely dump RAM of a Linux client and create a volatility profile for later analysis on your local host.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/limeaide) (version `v2.0.0.r3.gce3c9b7`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S limeaide    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/kd8bny/LiMEaide.git
cd LiMEaide
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `paramiko`, `termcolor`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[limeaide]] · [[BlackArch Tools]]

