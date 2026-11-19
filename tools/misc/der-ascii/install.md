---
tool: "der-ascii"
build-system: "go"
upstream: "https://github.com/google/der-ascii"
recipe: "blackarch"
nix-package: "der-ascii"
source: "https://github.com/google/der-ascii"
license: "Apache"
blackarch-pkgver: "109.5d9afa7"
provides: ["ascii2der", "der2ascii"]
---

# Installing der-ascii

> A reversible DER and BER pretty-printer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/der-ascii) (version `109.5d9afa7`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S der-ascii    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/google/der-ascii/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/google/der-ascii) (the recipe uses a versioned archive).

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
    -o ascii2der ./cmd/ascii2der

  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o der2ascii ./cmd/der2ascii
```

## Installed layout (per recipe)

- Commands: `/usr/bin/ascii2der`, `/usr/bin/der2ascii`

Backlinks: [[misc]] · [[der-ascii]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#der-ascii    # ad-hoc run, no install
nix profile install nixpkgs#der-ascii   # or persist it
```
