---
tool: "rfidtool"
build-system: "make"
upstream: "http://www.bindshell.net/tools/rfidtool.html"
recipe: "blackarch"
source: "http://www.bindshell.net/tools/rfidtool.html"
license: "GPL-2.0-or-later"
blackarch-pkgver: "0.2"
---

# Installing rfidtool

> An open source tool to read / write rfid tags.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rfidtool) (version `0.2`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S rfidtool    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.bindshell.net/tools/rfidtool.html) (the recipe uses a versioned archive).

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

Backlinks: [[wireless]] · [[rfidtool]] · [[BlackArch Tools]]

