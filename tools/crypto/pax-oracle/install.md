---
tool: "pax-oracle"
build-system: "go"
upstream: "https://github.com/liamg/pax"
recipe: "blackarch"
source: "https://github.com/liamg/pax"
license: "MIT"
blackarch-pkgver: "v0.2.2.r1.g38ac0d8"
---

# Installing pax-oracle

> CLI tool for PKCS7 padding oracle attacks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pax-oracle) (version `v0.2.2.r1.g38ac0d8`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pax-oracle    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/liamg/pax/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/liamg/pax) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $pkgname

  GOPATH="$srcdir" go mod download
  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o $pkgname ./cmd/pax
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[pax-oracle]] · [[BlackArch Tools]]

