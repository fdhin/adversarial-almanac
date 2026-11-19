---
tool: "mutator"
build-system: "make"
upstream: "https://bitbucket.org/alone/mutator/"
recipe: "blackarch"
aur-package: "mutator"
source: "https://bitbucket.org/alone/mutator/"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v0.2.2.r2.g164132d"
---

# Installing mutator

> This project aims to be a wordlist mutator with hormones, which means that some mutations will be applied to the result of the ones that have been already done, resulting in something like: corporation -> C0rp0r4t10n_2012

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mutator) (version `v0.2.2.r2.g164132d`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S mutator    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://bitbucket.org/alone/mutator/) (the recipe uses a versioned archive).

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

Backlinks: [[automation]] · [[mutator]] · [[BlackArch Tools]]

