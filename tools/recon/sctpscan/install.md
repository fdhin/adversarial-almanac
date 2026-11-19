---
tool: "sctpscan"
build-system: "make"
upstream: "http://www.p1sec.com/"
recipe: "blackarch"
source: "http://www.p1sec.com/"
license: "GPL3"
blackarch-pkgver: "34.4d44706"
kali-package: "sctpscan"
kali-version: "0.1-1kali5+b1"
---

# Installing sctpscan

> A network scanner for discovery and security.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sctpscan) (version `34.4d44706`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S sctpscan    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install sctpscan    # Kali package version 0.1-1kali5+b1
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.p1sec.com/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glib2`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[sctpscan]] · [[BlackArch Tools]]

