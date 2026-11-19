---
tool: "spike-fuzzer"
build-system: "autotools"
upstream: "http://www.immunitysec.com/resources-freesoftware.shtml"
recipe: "blackarch"
source: "http://www.immunitysec.com/resources-freesoftware.shtml"
license: "GPL2"
blackarch-pkgver: "2.9"
---

# Installing spike-fuzzer

> IMMUNITYsec's fuzzer creation kit in C.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/spike-fuzzer) (version `2.9`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S spike-fuzzer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.immunitysec.com/resources-freesoftware.shtml) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `autoconf`, `automake`, `gcc`
- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd SPIKE/SPIKE/src

  #aclocal
  #automake --add-missing
  #autoconf

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[spike-fuzzer]] · [[BlackArch Tools]]

