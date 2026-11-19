---
tool: "checkiban"
build-system: "make"
upstream: "http://kernel.embedromix.ro/us/"
recipe: "blackarch"
source: "http://kernel.embedromix.ro/us/"
license: "LGPL"
blackarch-pkgver: "0.2"
---

# Installing checkiban

> Checks the validity of an International Bank Account Number (IBAN).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/checkiban) (version `0.2`, license LGPL).

## Arch / BlackArch

```bash
sudo pacman -S checkiban    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://kernel.embedromix.ro/us/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "checkIBAN-$pkgver"

  make clean && make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[checkiban]] · [[BlackArch Tools]]

