---
tool: "quicksand-lite"
build-system: "other"
upstream: "https://github.com/tylabs/quicksand_lite"
recipe: "blackarch"
source: "https://github.com/tylabs/quicksand_lite"
license: "MPL2"
blackarch-pkgver: "33.c3edf92"
provides: ["quicksand"]
---

# Installing quicksand-lite

> Command line tool for scanning streams within office documents plus xor db attack.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/quicksand-lite) (version `33.c3edf92`, license MPL2).

## Arch / BlackArch

```bash
sudo pacman -S quicksand-lite    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/tylabs/quicksand_lite.git
cd quicksand_lite
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libzip`
  - `yara`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  gcc -g -o quicksand quicksand.c -L/usr/lib -lyara -lzip -lz
```

## Installed layout (per recipe)

- Commands: `/usr/bin/quicksand`

Backlinks: [[defensive]] · [[quicksand-lite]] · [[BlackArch Tools]]

