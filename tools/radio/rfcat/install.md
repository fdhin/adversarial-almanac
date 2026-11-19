---
tool: "rfcat"
build-system: "python"
upstream: "https://code.google.com/archive/p/rfcat/"
recipe: "blackarch"
source: "https://code.google.com/archive/p/rfcat/"
license: "BSD-3-Clause, GPL-2.0-or-later"
blackarch-pkgver: "v3.0.0.r3.gcf7a4f9"
kali-package: "rfcat"
kali-version: "1:3.0.0-0kali1"
---

# Installing rfcat

> Swiss-army knife of ISM band radio.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rfcat) (version `v3.0.0.r3.gcf7a4f9`, license BSD-3-Clause, GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S rfcat    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install rfcat    # Kali package version 1:3.0.0-0kali1
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/archive/p/rfcat/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `future`, `pyusb`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libusb` — Debian libusb-1.0-0-dev / Fedora libusbx-devel
  - `pyside6`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[radio]] · [[rfcat]] · [[BlackArch Tools]]

