---
tool: "rsakeyfind"
build-system: "make"
upstream: "http://citp.princeton.edu/memory/code/"
recipe: "blackarch"
source: "http://citp.princeton.edu/memory/code/"
license: "BSD"
blackarch-pkgver: "1.0"
kali-package: "rsakeyfind"
kali-version: "1:1.0-8"
---

# Installing rsakeyfind

> A tool to find RSA key in RAM.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rsakeyfind) (version `1.0`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S rsakeyfind    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install rsakeyfind    # Kali package version 1:1.0-8
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://citp.princeton.edu/memory/code/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `make`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[rsakeyfind]] · [[BlackArch Tools]]

