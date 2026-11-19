---
tool: "scamper"
build-system: "autotools"
upstream: "http://www.caida.org/tools/measurement/scamper/"
recipe: "blackarch"
aur-package: "scamper"
source: "http://www.caida.org/tools/measurement/scamper/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "20240920"
---

# Installing scamper

> A tool that actively probes the Internet in order to analyze topology and performance.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/scamper) (version `20240920`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S scamper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.caida.org/tools/measurement/scamper/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bzip2`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `xz`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-cvs-$pkgver"

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[scamper]] · [[BlackArch Tools]]

