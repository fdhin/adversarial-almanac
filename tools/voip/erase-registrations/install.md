---
tool: "erase-registrations"
build-system: "make"
upstream: "http://www.hackingexposedvoip.com/"
recipe: "blackarch"
source: "http://www.hackingexposedvoip.com/tools/erase_registrations.tar.gz"
license: "GPL"
blackarch-pkgver: "1.0"
---

# Installing erase-registrations

> An IAX flooder.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/erase-registrations) (version `1.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S erase-registrations    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
curl -fLO http://www.hackingexposedvoip.com/tools/erase_registrations.tar.gz
tar xf erase_registrations.tar.gz
cd erase_registrations
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `gcc`, `make`
- **System libraries** (Arch name — translate for your distro):
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel

## Exact build steps from the recipe

```bash
cd hack_library

  sed -i "s:gcc :gcc $CFLAGS :" Makefile

  make

  cd "$srcdir/erase_registrations"

  sed -i "s:gcc :gcc $CFLAGS :" Makefile

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[voip]] · [[erase-registrations]] · [[BlackArch Tools]]

