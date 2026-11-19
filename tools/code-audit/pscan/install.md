---
tool: "pscan"
build-system: "make"
upstream: "http://deployingradius.com/pscan/"
recipe: "blackarch"
source: "http://deployingradius.com/pscan/"
license: "GPL3"
blackarch-pkgver: "1.3"
---

# Installing pscan

> A limited problem scanner for C source files

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pscan) (version `1.3`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://deployingradius.com/pscan/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[pscan]] · [[BlackArch Tools]]

