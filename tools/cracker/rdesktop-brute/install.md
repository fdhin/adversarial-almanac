---
tool: "rdesktop-brute"
build-system: "autotools"
upstream: "http://www.rdesktop.org/"
recipe: "blackarch"
source: "http://www.foofus.net/~jmk/tools/rdp-brute-force-r805.diff"
license: "GPL-3.0-only"
blackarch-pkgver: "1.5.0"
---

# Installing rdesktop-brute

> It connects to windows terminal servers - Bruteforce patch included.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rdesktop-brute) (version `1.5.0`, license GPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S rdesktop-brute    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.rdesktop.org/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libao`
  - `libsamplerate`
  - `libx11`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  if [[ "$CARCH" == "x86_64" ]];
  then
    ./configure --prefix=/usr
  else
    ./configure --build=arm --prefix=/usr
  fi

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[rdesktop-brute]] · [[BlackArch Tools]]

