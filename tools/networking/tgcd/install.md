---
tool: "tgcd"
build-system: "autotools"
upstream: "https://sourceforge.net/projects/tgcd/files/"
recipe: "blackarch"
aur-package: "tgcd"
source: "https://sourceforge.net/projects/tgcd/files/"
license: "custom:unknown"
blackarch-pkgver: "1.1.1"
---

# Installing tgcd

> TCP/IP Gender Changer Daemon utility.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tgcd) (version `1.1.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S tgcd    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://sourceforge.net/projects/tgcd/files/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[tgcd]] · [[BlackArch Tools]]

