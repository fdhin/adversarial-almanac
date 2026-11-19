---
tool: "gospider"
build-system: "go"
upstream: "https://github.com/jaeles-project/gospider"
recipe: "blackarch"
nix-package: "gospider"
source: "https://github.com/jaeles-project/gospider"
license: "MIT"
blackarch-pkgver: "108.f6cc9a7"
---

# Installing gospider

> Fast web spider written in Go.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gospider) (version `108.f6cc9a7`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S gospider    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/jaeles-project/gospider/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/jaeles-project/gospider) (the recipe uses a versioned archive).

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
    -o $pkgname .
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[gospider]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#gospider    # ad-hoc run, no install
nix profile install nixpkgs#gospider   # or persist it
```
