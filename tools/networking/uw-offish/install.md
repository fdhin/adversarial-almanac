---
tool: "uw-offish"
build-system: "make"
upstream: "http://uberwall.org/bin/download/42/UW_offish-0.1.tar.gz"
recipe: "blackarch"
source: "http://uberwall.org/bin/download/42/UW_offish-0.1.tar.gz"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing uw-offish

> Clear-text protocol simulator.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/uw-offish) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S uw-offish    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://uberwall.org/bin/download/42/UW_offish.1.tar.gz) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "UW_offish-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[uw-offish]] · [[BlackArch Tools]]

