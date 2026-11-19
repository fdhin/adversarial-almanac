---
tool: "maryam"
build-system: "scripts"
upstream: "https://github.com/saeeddhqan/Maryam"
recipe: "blackarch"
source: "https://github.com/saeeddhqan/Maryam"
license: "MIT"
blackarch-pkgver: "v.2.5.3.r0.g2bb4ea2"
kali-package: "maryam"
kali-version: "2.5.3-0kali2"
---

# Installing maryam

> OSINT Framework

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/maryam) (version `v.2.5.3.r0.g2bb4ea2`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S maryam    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install maryam    # Kali package version 2.5.3-0kali2
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/saeeddhqan/Maryam.git
cd Maryam
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `virtualenv`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[maryam]] · [[BlackArch Tools]]

