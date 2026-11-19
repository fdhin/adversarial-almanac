---
tool: "pipal"
build-system: "scripts"
upstream: "https://github.com/digininja/pipal"
recipe: "blackarch"
source: "https://github.com/digininja/pipal"
license: "GPL3"
blackarch-pkgver: "3.4.0.r9.g392f0aa"
kali-package: "pipal"
kali-version: "3.4.0-0kali1"
---

# Installing pipal

> A password analyser.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pipal) (version `3.4.0.r9.g392f0aa`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pipal    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install pipal    # Kali package version 3.4.0-0kali1
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/digininja/pipal) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[pipal]] · [[BlackArch Tools]]

