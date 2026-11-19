---
tool: "dracnmap"
build-system: "scripts"
upstream: "https://github.com/screetsec/Dracnmap"
recipe: "blackarch"
source: "https://github.com/screetsec/Dracnmap"
license: "MIT"
blackarch-pkgver: "69.09d3945"
provides: ["dracnmap-drac0s"]
---

# Installing dracnmap

> Tool to exploit the network and gathering information with nmap help.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dracnmap) (version `69.09d3945`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S dracnmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/screetsec/Dracnmap.git
cd Dracnmap
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `firefox`
  - `nano`
  - `nmap`
  - `xterm`

## Installed layout (per recipe)

- Commands: `/usr/bin/dracnmap-drac0s`

Backlinks: [[automation]] · [[dracnmap]] · [[BlackArch Tools]]

