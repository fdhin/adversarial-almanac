---
tool: "icmptx"
build-system: "make"
upstream: "http://thomer.com/icmptx/"
recipe: "blackarch"
source: "http://thomer.com/icmptx/"
license: "GPL-3.0-only"
blackarch-pkgver: "0.2.r12.g2235866"
---

# Installing icmptx

> IP over ICMP tunnel.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/icmptx) (version `0.2.r12.g2235866`, license GPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S icmptx    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://thomer.com/icmptx/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `gcc`, `git`, `make`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd $pkgname

  make flags="$CFLAGS"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[tunnel]] · [[icmptx]] · [[BlackArch Tools]]

