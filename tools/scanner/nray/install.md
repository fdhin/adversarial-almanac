---
tool: "nray"
build-system: "go"
upstream: "https://github.com/nray-scanner/nray"
recipe: "blackarch"
nix-package: "nray"
source: "https://github.com/nray-scanner/nray"
license: "GPL3"
blackarch-pkgver: "99.f5eeb37"
---

# Installing nray

> Distributed port scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nray) (version `99.f5eeb37`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S nray    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/nray-scanner/nray/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/nray-scanner/nray) (the recipe uses a versioned archive).

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

Backlinks: [[scanner]] · [[nray]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#nray    # ad-hoc run, no install
nix profile install nixpkgs#nray   # or persist it
```
