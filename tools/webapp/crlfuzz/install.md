---
tool: "crlfuzz"
build-system: "go"
upstream: "https://github.com/dwisiswant0/crlfuzz"
recipe: "blackarch"
nix-package: "crlfuzz"
source: "https://github.com/dwisiswant0/crlfuzz"
license: "MIT"
blackarch-pkgver: "62.7a442bb"
---

# Installing crlfuzz

> A fast tool to scan CRLF vulnerability written in Go.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/crlfuzz) (version `62.7a442bb`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S crlfuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/dwisiswant0/crlfuzz/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/dwisiswant0/crlfuzz) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $pkgname

  GOPATH="$srcdir" go mod tidy
  GOPATH="$srcdir" go mod download

  cd "cmd/$pkgname"

  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o $pkgname .
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[crlfuzz]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#crlfuzz    # ad-hoc run, no install
nix profile install nixpkgs#crlfuzz   # or persist it
```
