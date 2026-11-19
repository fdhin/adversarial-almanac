---
tool: "logkeys"
build-system: "autotools"
upstream: "https://github.com/kernc/logkeys"
recipe: "blackarch"
nix-package: "logkeys"
aur-package: "logkeys"
source: "https://github.com/kernc/logkeys"
license: "GPL3"
blackarch-pkgver: "97.98aac72"
---

# Installing logkeys

> A GNU/Linux keylogger that worked.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/logkeys) (version `97.98aac72`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S logkeys    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/kernc/logkeys) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `kbd`
  - `procps-ng`

## Exact build steps from the recipe

```bash
cd "$pkgname/build"

  ../configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[keylogger]] · [[logkeys]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#logkeys    # ad-hoc run, no install
nix profile install nixpkgs#logkeys   # or persist it
```
