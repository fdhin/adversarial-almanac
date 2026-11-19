---
tool: "bluebugger"
build-system: "make"
upstream: "http://packetstormsecurity.com/files/54024/bluebugger-0.1.tar.gz.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/54024/bluebugger-0.1.tar.gz.html"
license: "GPL"
blackarch-pkgver: "0.1"
---

# Installing bluebugger

> An implementation of the bluebug technique which was discovered by Martin Herfurt.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bluebugger) (version `0.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S bluebugger    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/54024/bluebugger.1.tar.gz.html) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bluez-libs` — Debian libbluetooth-dev / Fedora bluez-libs-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[bluetooth]] · [[bluebugger]] · [[BlackArch Tools]]

