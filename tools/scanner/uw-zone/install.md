---
tool: "uw-zone"
build-system: "make"
upstream: "http://uberwall.org/bin/download/43/UWzone.tgz"
recipe: "blackarch"
source: "http://uberwall.org/bin/download/download/43/UWzone.tgz"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing uw-zone

> Multi threaded, randomized IP zoner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/uw-zone) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S uw-zone    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
curl -fLO http://uberwall.org/bin/download/download/43/UWzone.tgz
tar xf UWzone.tgz
cd UWzone
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd UWzone

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[uw-zone]] · [[BlackArch Tools]]

