---
tool: "metoscan"
build-system: "make"
upstream: "http://www.open-labs.org/"
recipe: "blackarch"
source: "http://www.open-labs.org/"
license: "custom"
blackarch-pkgver: "05"
---

# Installing metoscan

> Tool for scanning the HTTP methods supported by a webserver.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/metoscan) (version `05`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S metoscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.open-labs.org/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `curl`

## Exact build steps from the recipe

```bash
cd "${pkgname}${pkgver}"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[metoscan]] · [[BlackArch Tools]]

