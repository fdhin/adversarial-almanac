---
tool: "firmware-mod-kit"
build-system: "make"
upstream: "https://github.com/rampageX/firmware-mod-kit"
recipe: "blackarch"
aur-package: "firmware-mod-kit"
source: "https://github.com/rampageX/firmware-mod-kit"
license: "MIT"
blackarch-pkgver: "0.99.r119.gc72f45d"
kali-package: "firmware-mod-kit"
kali-version: "0.99+git20190714-0kali4"
---

# Installing firmware-mod-kit

> Modify firmware images without recompiling.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/firmware-mod-kit) (version `0.99.r119.gc72f45d`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S firmware-mod-kit    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install firmware-mod-kit    # Kali package version 0.99+git20190714-0kali4
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/rampageX/firmware-mod-kit) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Python (pip)**: `magic`
- **Build tools**: `gcc`, `git`, `make`

## Exact build steps from the recipe

```bash
cd "$pkgname/src"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[firmware]] · [[firmware-mod-kit]] · [[BlackArch Tools]]

