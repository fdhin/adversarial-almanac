---
tool: "pemcracker"
build-system: "make"
upstream: "https://github.com/bwall/pemcracker.git"
recipe: "blackarch"
source: "https://github.com/bwall/pemcracker.git"
license: "MIT"
blackarch-pkgver: "9.a741c93"
---

# Installing pemcracker

> Tool to crack encrypted PEM files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pemcracker) (version `9.a741c93`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pemcracker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/bwall/pemcracker.git) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[pemcracker]] · [[BlackArch Tools]]

