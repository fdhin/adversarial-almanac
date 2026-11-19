---
tool: "wificurse"
build-system: "make"
upstream: "https://github.com/oblique/wificurse"
recipe: "blackarch"
aur-package: "wificurse"
source: "https://github.com/oblique/wificurse"
license: "GPL3"
blackarch-pkgver: "0.3.9"
---

# Installing wificurse

> WiFi jamming tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wificurse) (version `0.3.9`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S wificurse    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/oblique/wificurse) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[wificurse]] · [[BlackArch Tools]]

