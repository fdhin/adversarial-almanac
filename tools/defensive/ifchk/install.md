---
tool: "ifchk"
build-system: "make"
upstream: "http://www.noorg.org/ifchk/"
recipe: "blackarch"
source: "http://www.noorg.org/ifchk/"
license: "GPL2"
blackarch-pkgver: "1.1.3"
---

# Installing ifchk

> A network interface promiscuous mode detection tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ifchk) (version `1.1.3`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S ifchk    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.noorg.org/ifchk/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `sysfsutils`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[defensive]] · [[ifchk]] · [[BlackArch Tools]]

