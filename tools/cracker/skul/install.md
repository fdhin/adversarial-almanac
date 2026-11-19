---
tool: "skul"
build-system: "autotools"
upstream: "https://github.com/cryptcoffee/skul"
recipe: "blackarch"
source: "https://github.com/cryptcoffee/skul"
license: "GPL2"
blackarch-pkgver: "27.7bd83f1"
---

# Installing skul

> A PoC to bruteforce the Cryptsetup implementation of Linux Unified Key Setup (LUKS).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/skul) (version `27.7bd83f1`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S skul    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/cryptcoffee/skul) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  ./configure

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[skul]] · [[BlackArch Tools]]

