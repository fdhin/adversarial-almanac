---
tool: "jwt-cracker"
build-system: "make"
upstream: "https://github.com/brendan-rius/c-jwt-cracker"
recipe: "blackarch"
source: "https://github.com/brendan-rius/c-jwt-cracker"
license: "MIT"
blackarch-pkgver: "23.8130879"
---

# Installing jwt-cracker

> JWT brute force cracker written in C.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jwt-cracker) (version `23.8130879`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S jwt-cracker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/brendan-rius/c-jwt-cracker.git
cd c-jwt-cracker
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

Backlinks: [[cracker]] · [[jwt-cracker]] · [[BlackArch Tools]]

