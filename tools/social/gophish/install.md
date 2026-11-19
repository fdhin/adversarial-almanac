---
tool: "gophish"
build-system: "go"
upstream: "https://github.com/gophish/gophish"
recipe: "blackarch"
nix-package: "gophish"
aur-package: "gophish"
source: "https://github.com/gophish/gophish"
license: "MIT"
blackarch-pkgver: "815.95618469"
---

# Installing gophish

> Open-Source Phishing Framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gophish) (version `815.95618469`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S gophish    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/gophish/gophish/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/gophish/gophish) (the recipe uses a versioned archive).

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

Backlinks: [[social]] · [[gophish]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#gophish    # ad-hoc run, no install
nix profile install nixpkgs#gophish   # or persist it
```
