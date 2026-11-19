---
tool: "sparrow-wifi"
build-system: "scripts"
upstream: "https://github.com/ghostop14/sparrow-wifi"
recipe: "blackarch"
source: "https://github.com/ghostop14/sparrow-wifi"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v2.0.r31.gb4f4592"
---

# Installing sparrow-wifi

> Next-Gen GUI-based WiFi and Bluetooth Analyzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sparrow-wifi) (version `v2.0.r31.gb4f4592`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S sparrow-wifi    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ghostop14/sparrow-wifi) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `gpsd`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[bluetooth]] · [[sparrow-wifi]] · [[BlackArch Tools]]

