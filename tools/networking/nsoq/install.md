---
tool: "nsoq"
build-system: "make"
upstream: "https://github.com/FelipeEcker/nsoq/tags"
recipe: "blackarch"
aur-package: "nsoq"
source: "https://github.com/FelipeEcker/nsoq/tags"
license: "BSD"
blackarch-pkgver: "1.9.5"
---

# Installing nsoq

> A Network Security Tool for packet manipulation that allows a large number of options.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nsoq) (version `1.9.5`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S nsoq    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.nsoq.org/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[networking]] · [[nsoq]] · [[BlackArch Tools]]

