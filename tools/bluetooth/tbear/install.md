---
tool: "tbear"
build-system: "make"
upstream: "http://freshmeat.net/projects/t-bear"
recipe: "blackarch"
source: "http://freshmeat.net/projects/t-bear"
license: "GPL3"
blackarch-pkgver: "1.5"
---

# Installing tbear

> Transient Bluetooth Environment Auditor includes an ncurses-based Bluetooth scanner (a bit similar to kismet), a Bluetooth DoS tool, and a Bluetooth hidden device locator.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tbear) (version `1.5`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S tbear    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://freshmeat.net/projects/t-bear) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bluez`
  - `bluez-libs` — Debian libbluetooth-dev / Fedora bluez-libs-devel
  - `ncurses`
  - `ncurses5-compat-libs`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make CFLAGS="$CFLAGS"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[bluetooth]] · [[tbear]] · [[BlackArch Tools]]

