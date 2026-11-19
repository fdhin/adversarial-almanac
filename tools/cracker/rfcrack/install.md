---
tool: "rfcrack"
build-system: "scripts"
upstream: "https://github.com/cclabsInc/RFCrack"
recipe: "blackarch"
source: "https://github.com/cclabsInc/RFCrack"
license: "custom:unknown"
blackarch-pkgver: "128.db73830"
---

# Installing rfcrack

> A Software Defined Radio Attack Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rfcrack) (version `128.db73830`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S rfcrack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/cclabsInc/RFCrack) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `bitstring`, `libusb`, `matplotlib`, `numpy`, `pip`, `pyusb`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `rfcat`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[rfcrack]] · [[BlackArch Tools]]

