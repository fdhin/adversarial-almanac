---
tool: "x8"
build-system: "go"
upstream: "https://github.com/Sh1Yo/x8"
recipe: "blackarch"
source: "https://github.com/Sh1Yo/x8"
license: "custom:unknown"
blackarch-pkgver: "v4.3.0.r10.gc78f246"
---

# Installing x8

> Hidden parameters discovery suite.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/x8) (version `v4.3.0.r10.gc78f246`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S x8    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/Sh1Yo/x8/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/Sh1Yo/x8) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `cargo`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  CFLAGS+=' -ffat-lto-objects'
  cargo build --release --locked
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[x8]] · [[BlackArch Tools]]

