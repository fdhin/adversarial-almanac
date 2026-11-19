---
tool: "schnappi-dhcp"
build-system: "scripts"
upstream: "http://www.emanuelegentili.eu/"
recipe: "blackarch"
source: "http://www.emanuelegentili.eu/"
license: "GPL"
blackarch-pkgver: "0.1"
---

# Installing schnappi-dhcp

> Can fuck network with no DHCP.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/schnappi-dhcp) (version `0.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S schnappi-dhcp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.emanuelegentili.eu/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bash`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[schnappi-dhcp]] · [[BlackArch Tools]]

