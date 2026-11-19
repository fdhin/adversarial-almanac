---
tool: "ppfuzz"
build-system: "go"
upstream: "https://github.com/dwisiswant0/ppfuzz"
recipe: "blackarch"
source: "https://github.com/dwisiswant0/ppfuzz"
license: "MIT"
blackarch-pkgver: "31.80982ec"
---

# Installing ppfuzz

> A fast tool to scan client-side prototype pollution vulnerability written in Rust.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ppfuzz) (version `31.80982ec`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S ppfuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/dwisiswant0/ppfuzz/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/dwisiswant0/ppfuzz) (the recipe uses a versioned archive).

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

Backlinks: [[webapp]] · [[ppfuzz]] · [[BlackArch Tools]]

