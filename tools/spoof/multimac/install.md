---
tool: "multimac"
build-system: "make"
upstream: "http://sourceforge.net/projects/multimac/"
recipe: "blackarch"
source: "http://sourceforge.net/projects/multimac/"
license: "GPL2"
blackarch-pkgver: "1.0.3"
---

# Installing multimac

> Multiple MACs on an adapter.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/multimac) (version `1.0.3`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S multimac    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/multimac/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `gcc`

## Exact build steps from the recipe

```bash
cd $pkgname

  make all
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[spoof]] · [[multimac]] · [[BlackArch Tools]]

