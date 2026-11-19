---
tool: "nfcutils"
build-system: "autotools"
upstream: "https://github.com/nfc-tools/nfcutils"
recipe: "blackarch"
source: "https://github.com/nfc-tools/nfcutils"
license: "LGPL-3.0-only"
blackarch-pkgver: "61.49aa178"
---

# Installing nfcutils

> A simple command that lists tags which are in your NFC device field.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nfcutils) (version `61.49aa178`, license LGPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S nfcutils    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/nfc-tools/nfcutils) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `autoconf`, `automake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `libnfc`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[nfc]] · [[nfcutils]] · [[BlackArch Tools]]

