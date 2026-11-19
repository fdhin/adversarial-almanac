---
tool: "yate-bts"
build-system: "autotools"
upstream: "https://yatebts.com/"
recipe: "blackarch"
aur-package: "yate-bts"
source: "https://yatebts.com/"
license: "GPL"
blackarch-pkgver: "6.1.0"
---

# Installing yate-bts

> An open source GSM Base Station software.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/yate-bts) (version `6.1.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S yate-bts    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://yatebts.com/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `yate`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./configure --prefix=/usr \
              --sysconfdir=/etc
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[radio]] · [[yate-bts]] · [[BlackArch Tools]]

