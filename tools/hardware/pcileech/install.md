---
tool: "pcileech"
build-system: "scripts"
upstream: "https://github.com/ufrisk/pcileech/releases"
recipe: "blackarch"
source: "https://github.com/ufrisk/pcileech/releases"
license: "AGPL-3.0-only"
blackarch-pkgver: "4.19.5"
---

# Installing pcileech

> Tool, which uses PCIe hardware devices to read and write from the target system memory.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pcileech) (version `4.19.5`, license AGPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S pcileech    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ufrisk/pcileech/releases) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `leechcore`
  - `libusb` — Debian libusb-1.0-0-dev / Fedora libusbx-devel

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[hardware]] · [[pcileech]] · [[BlackArch Tools]]

