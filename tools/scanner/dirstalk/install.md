---
tool: "dirstalk"
build-system: "go"
upstream: "https://github.com/stefanoj3/dirstalk"
recipe: "blackarch"
nix-package: "dirstalk"
aur-package: "dirstalk"
source: "https://github.com/stefanoj3/dirstalk"
license: "MIT"
blackarch-pkgver: "1.3.3"
---

# Installing dirstalk

> Modern alternative to dirbuster/dirb.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dirstalk) (version `1.3.3`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S dirstalk    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/stefanoj3/dirstalk/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/stefanoj3/dirstalk) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $pkgname-$pkgver

  GOPATH="$srcdir" go mod download
  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o $pkgname ./cmd/$pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[dirstalk]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dirstalk    # ad-hoc run, no install
nix profile install nixpkgs#dirstalk   # or persist it
```
