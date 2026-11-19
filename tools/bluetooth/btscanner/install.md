---
tool: "btscanner"
build-system: "autotools"
upstream: "http://www.pentest.co.uk"
recipe: "blackarch"
aur-package: "btscanner"
source: "http://www.pentest.co.uk"
license: "GPL"
blackarch-pkgver: "2.1"
kali-package: "btscanner"
kali-version: "2.1-10"
---

# Installing btscanner

> Bluetooth device scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/btscanner) (version `2.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S btscanner    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install btscanner    # Kali package version 2.1-10
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.pentest.co.uk) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bluez-libs` — Debian libbluetooth-dev / Fedora bluez-libs-devel
  - `libxml2` — Debian libxml2-dev / Fedora libxml2-devel
  - `ncurses5-compat-libs`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr --sysconf=/etc/btscanner \
    --datadir=/usr/share/btscanner

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[bluetooth]] · [[btscanner]] · [[BlackArch Tools]]

