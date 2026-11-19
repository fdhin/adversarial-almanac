---
tool: "fernmelder"
build-system: "make"
upstream: "https://github.com/stealth/fernmelder"
recipe: "blackarch"
source: "https://github.com/stealth/fernmelder"
license: "GPL-3.0-or-later"
blackarch-pkgver: "0.4s2.r2.g030212e"
---

# Installing fernmelder

> Asynchronous mass DNS scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fernmelder) (version `0.4s2.r2.g030212e`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S fernmelder    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/stealth/fernmelder) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[fernmelder]] · [[BlackArch Tools]]

