---
tool: "nimux"
build-system: "other"
upstream: "https://github.com/blue0x1/nimux"
recipe: "blackarch"
aur-package: "nimux"
source: "https://github.com/blue0x1/nimux.git"
license: "AGPL-3.0-only"
blackarch-pkgver: "1.0.7.r2.g7c2d309"
provides: ["nimux"]
---

# Installing nimux

> Pure-Nim network enumeration and remote execution toolkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nimux) (version `1.0.7.r2.g7c2d309`, license AGPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S nimux    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/blue0x1/nimux.git
cd nimux
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `krb5`
  - `nim`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel
  - `zstd`

## Exact build steps from the recipe

```bash
cd $pkgname

  nim c -d:release --opt:speed -o:nimux src/nimux.nim
```

## Installed layout (per recipe)

- Commands: `/usr/bin/nimux`

Backlinks: [[scanner]] · [[nimux]] · [[BlackArch Tools]]

