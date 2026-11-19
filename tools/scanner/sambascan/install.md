---
tool: "sambascan"
build-system: "autotools"
upstream: "http://sourceforge.net/projects/sambascan2/"
recipe: "blackarch"
source: "http://sourceforge.net/projects/sambascan2/"
license: "GPL2"
blackarch-pkgver: "0.5.0"
---

# Installing sambascan

> Allows you to search an entire network or a number of hosts for SMB shares. It will also list the contents of all public shares that it finds.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sambascan) (version `0.5.0`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S sambascan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/sambascan2/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `nmap`
  - `samba`

## Exact build steps from the recipe

```bash
cd "${pkgname}2-$pkgver"

  ./configure --prefix=/usr
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[sambascan]] · [[BlackArch Tools]]

