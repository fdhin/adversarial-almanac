---
tool: "hetty"
build-system: "go"
upstream: "https://github.com/dstotijn/hetty"
recipe: "blackarch"
aur-package: "hetty"
source: "https://github.com/dstotijn/hetty"
license: "MIT"
blackarch-pkgver: "v0.7.0.r7.gf7def87"
---

# Installing hetty

> HTTP toolkit for security research. Aims to become an open source alternative to commercial software like Burp Suite Pro.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hetty) (version `v0.7.0.r7.gf7def87`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S hetty    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/dstotijn/hetty/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/dstotijn/hetty) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `yarn`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[hetty]] · [[BlackArch Tools]]

