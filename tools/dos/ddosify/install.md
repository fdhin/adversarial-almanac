---
tool: "ddosify"
build-system: "go"
upstream: "https://github.com/ddosify/ddosify"
recipe: "blackarch"
nix-package: "ddosify"
source: "https://github.com/ddosify/ddosify"
license: "AGPL"
blackarch-pkgver: "v2.6.0.r32.g5cf7df3"
---

# Installing ddosify

> High-performance load testing tool, written in Golang.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ddosify) (version `v2.6.0.r32.g5cf7df3`, license AGPL).

## Arch / BlackArch

```bash
sudo pacman -S ddosify    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/ddosify/ddosify/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/ddosify/ddosify) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd "$pkgname/${pkgname}_engine"

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

Backlinks: [[dos]] · [[ddosify]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ddosify    # ad-hoc run, no install
nix profile install nixpkgs#ddosify   # or persist it
```
