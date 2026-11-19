---
tool: "onetwopunch"
build-system: "scripts"
upstream: "https://github.com/superkojiman/onetwopunch"
recipe: "blackarch"
source: "https://github.com/superkojiman/onetwopunch"
license: "MIT"
blackarch-pkgver: "v1.0.0.r2.gd4ab4e8"
---

# Installing onetwopunch

> Use unicornscan to quickly scan all open ports, and then pass the open ports to nmap for detailed scans.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/onetwopunch) (version `v1.0.0.r2.gd4ab4e8`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S onetwopunch    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/superkojiman/onetwopunch) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `nmap`
  - `unicornscan`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[onetwopunch]] · [[BlackArch Tools]]

