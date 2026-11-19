---
tool: "proctal"
build-system: "autotools"
upstream: "https://github.com/daniel-araujo/proctal"
recipe: "blackarch"
source: "https://github.com/daniel-araujo/proctal"
license: "GPL3"
blackarch-pkgver: "477.f72cefe"
---

# Installing proctal

> Provides a command line interface and a C library to manipulate the address space of a running program on Linux.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/proctal) (version `477.f72cefe`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S proctal    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/daniel-araujo/proctal) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `capstone`
  - `keystone`
  - `php`
  - `yuck`

## Exact build steps from the recipe

```bash
cd $pkgname

  unset CPPFLAGS
  unset CFLAGS
  #autoreconf -i
  #./init
  ./bootstrap
  ./configure --prefix=/usr
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[proctal]] · [[BlackArch Tools]]

