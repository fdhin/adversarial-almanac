---
tool: "wirouter-keyrec"
build-system: "autotools"
upstream: "http://www.salvatorefresta.net/tools/"
recipe: "blackarch"
source: "http://www.salvatorefresta.net/tools/"
license: "GPL3"
blackarch-pkgver: "1.1.2"
---

# Installing wirouter-keyrec

> A platform independent software to recover the default WPA passphrases of the supported router models

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wirouter-keyrec) (version `1.1.2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S wirouter-keyrec    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.salvatorefresta.net/tools/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Exact build steps from the recipe

```bash
cd "WiRouter_KeyRec_$pkgver"

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[wirouter-keyrec]] · [[BlackArch Tools]]

