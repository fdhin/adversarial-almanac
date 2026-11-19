---
tool: "kickthemout"
build-system: "scripts"
upstream: "https://github.com/k4m4/kickthemout"
recipe: "blackarch"
source: "https://github.com/k4m4/kickthemout"
license: "MIT"
blackarch-pkgver: "212.861aea2"
---

# Installing kickthemout

> Kick devices off your network by performing an ARP Spoof attack.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kickthemout) (version `212.861aea2`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S kickthemout    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/k4m4/kickthemout) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `python-nmap`, `scapy`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `kamene`
  - `nmap`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[kickthemout]] · [[BlackArch Tools]]

