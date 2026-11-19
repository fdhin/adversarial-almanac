---
tool: "spektrum"
build-system: "scripts"
upstream: "https://github.com/pavels/spektrum"
recipe: "blackarch"
source: "https://github.com/pavels/spektrum"
license: "BSD-3"
blackarch-pkgver: "2.1.0"
---

# Installing spektrum

> rtl-sdr spectrum analyzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/spektrum) (version `2.1.0`, license BSD-3).

## Arch / BlackArch

```bash
sudo pacman -S spektrum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/pavels/spektrum) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `libusb` — Debian libusb-1.0-0-dev / Fedora libusbx-devel

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[radio]] · [[spektrum]] · [[BlackArch Tools]]

