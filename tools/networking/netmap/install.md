---
tool: "netmap"
build-system: "make"
upstream: "http://netmap.sourceforge.net/"
recipe: "blackarch"
aur-package: "netmap"
source: "http://netmap.sourceforge.net/"
license: "GPL"
blackarch-pkgver: "0.1.3"
provides: ["makelist"]
---

# Installing netmap

> Can be used to make a graphical representation of the surrounding network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netmap) (version `0.1.3`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S netmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://netmap.sourceforge.net/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `graphviz`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  sed -i "s|-O|$CXXFLAGS|g" belgolib/Makefile makelist/Makefile netmap/Makefile

  make CFLAGS="$CFLAGS"
```

## Installed layout (per recipe)

- Commands: `/usr/bin/makelist`

Backlinks: [[networking]] · [[netmap]] · [[BlackArch Tools]]

