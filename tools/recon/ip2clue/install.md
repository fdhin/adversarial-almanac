---
tool: "ip2clue"
build-system: "autotools"
upstream: "http://kernel.embedromix.ro/us/"
recipe: "blackarch"
source: "http://kernel.embedromix.ro/us/"
license: "GPL3"
blackarch-pkgver: "0.0.95"
---

# Installing ip2clue

> A small memory/CPU footprint daemon to lookup country (and other info) based on IP (v4 and v6).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ip2clue) (version `0.0.95`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S ip2clue    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://kernel.embedromix.ro/us/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `conn`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[ip2clue]] · [[BlackArch Tools]]

