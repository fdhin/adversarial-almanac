---
tool: "delldrac"
build-system: "scripts"
upstream: "https://www.trustedsec.com/september-2012/owning-dell-drac-awesome-hack/"
recipe: "blackarch"
source: "https://www.trustedsec.com/files/delldrac.py"
license: "custom"
blackarch-pkgver: "0.1a"
---

# Installing delldrac

> DellDRAC and Dell Chassis Discovery and Brute Forcer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/delldrac) (version `0.1a`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S delldrac    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.trustedsec.com/september/owning-dell-drac-awesome-hack/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[delldrac]] · [[BlackArch Tools]]

