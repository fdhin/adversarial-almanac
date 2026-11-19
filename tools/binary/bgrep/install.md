---
tool: "bgrep"
build-system: "make"
upstream: "https://github.com/tmbinc/bgrep"
recipe: "blackarch"
aur-package: "bgrep"
source: "https://github.com/tmbinc/bgrep"
license: "BSD"
blackarch-pkgver: "32.585041e"
---

# Installing bgrep

> Binary grep.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bgrep) (version `32.585041e`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S bgrep    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/tmbinc/bgrep) (the recipe uses a versioned archive).

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

Backlinks: [[binary]] · [[bgrep]] · [[BlackArch Tools]]

