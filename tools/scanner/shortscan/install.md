---
tool: "shortscan"
build-system: "go"
upstream: "https://github.com/bitquark/shortscan"
recipe: "blackarch"
nix-package: "shortscan"
source: "https://github.com/bitquark/shortscan"
license: "MIT"
blackarch-pkgver: "0.9.2.r0.g2ba744f"
provides: ["shortutil"]
---

# Installing shortscan

> An IIS short filename enumeration tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/shortscan) (version `0.9.2.r0.g2ba744f`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S shortscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/bitquark/shortscan/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/bitquark/shortscan) (the recipe uses a versioned archive).

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
    -o $pkgname "./cmd/$pkgname"
  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o shortutil ./cmd/shortutil
```

## Installed layout (per recipe)

- Commands: `/usr/bin/shortutil`

Backlinks: [[scanner]] · [[shortscan]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#shortscan    # ad-hoc run, no install
nix profile install nixpkgs#shortscan   # or persist it
```
