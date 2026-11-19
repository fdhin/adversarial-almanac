---
tool: "fssb"
build-system: "make"
upstream: "https://github.com/adtac/fssb"
recipe: "blackarch"
source: "https://github.com/adtac/fssb"
license: "GPL3"
blackarch-pkgver: "73.51d2ac2"
---

# Installing fssb

> A low-level filesystem sandbox for Linux using syscall intercepts.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fssb) (version `73.51d2ac2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S fssb    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/adtac/fssb) (the recipe uses a versioned archive).

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

Backlinks: [[defensive]] · [[fssb]] · [[BlackArch Tools]]

