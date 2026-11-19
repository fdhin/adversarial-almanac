---
tool: "udpastcp"
build-system: "make"
upstream: "https://github.com/Hello71/udpastcp"
recipe: "blackarch"
source: "https://github.com/Hello71/udpastcp"
license: "GPL2"
blackarch-pkgver: "29.683b5e3"
---

# Installing udpastcp

> This program hides UDP traffic as TCP traffic in order to bypass certain firewalls.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/udpastcp) (version `29.683b5e3`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S udpastcp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/Hello71/udpastcp) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libev`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[udpastcp]] · [[BlackArch Tools]]

