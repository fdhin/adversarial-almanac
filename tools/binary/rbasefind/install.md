---
tool: "rbasefind"
build-system: "go"
upstream: "https://github.com/sgayou/rbasefind"
recipe: "blackarch"
source: "https://github.com/sgayou/rbasefind"
license: "MIT"
blackarch-pkgver: "41.a661118"
---

# Installing rbasefind

> A firmware base address search tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rbasefind) (version `41.a661118`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S rbasefind    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/sgayou/rbasefind/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/sgayou/rbasefind) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `cargo`, `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  cargo build --release
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[rbasefind]] · [[BlackArch Tools]]

