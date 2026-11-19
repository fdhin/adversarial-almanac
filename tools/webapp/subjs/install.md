---
tool: "subjs"
build-system: "go"
upstream: "https://github.com/lc/subjs"
recipe: "blackarch"
nix-package: "subjs"
aur-package: "subjs"
source: "https://github.com/lc/subjs"
license: "MIT"
blackarch-pkgver: "48.3eb4dc9"
---

# Installing subjs

> Fetches javascript file from a list of URLS or subdomains.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/subjs) (version `48.3eb4dc9`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S subjs    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/lc/subjs/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/lc/subjs) (the recipe uses a versioned archive).

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

Backlinks: [[webapp]] · [[subjs]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#subjs    # ad-hoc run, no install
nix profile install nixpkgs#subjs   # or persist it
```
