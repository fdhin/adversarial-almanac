---
tool: "padre"
build-system: "go"
upstream: "https://github.com/glebarez/padre"
recipe: "blackarch"
nix-package: "padre"
source: "https://github.com/glebarez/padre"
license: "MIT"
blackarch-pkgver: "v2.2.0.r15.g9a2e1f2"
---

# Installing padre

> Padding Oracle attack tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/padre) (version `v2.2.0.r15.g9a2e1f2`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S padre    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/glebarez/padre/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/glebarez/padre) (the recipe uses a versioned archive).

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

Backlinks: [[crypto]] · [[padre]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#padre    # ad-hoc run, no install
nix profile install nixpkgs#padre   # or persist it
```
