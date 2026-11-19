---
tool: "cent"
build-system: "go"
upstream: "https://github.com/xm1k3/cent"
recipe: "blackarch"
nix-package: "cent"
source: "https://github.com/xm1k3/cent"
license: "Apache"
blackarch-pkgver: "v2.2.0.r0.gfcf1c1c"
---

# Installing cent

> Community edition nuclei templates.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cent) (version `v2.2.0.r0.gfcf1c1c`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S cent    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/xm1k3/cent/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/xm1k3/cent) (the recipe uses a versioned archive).

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

Backlinks: [[webapp]] · [[cent]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#cent    # ad-hoc run, no install
nix profile install nixpkgs#cent   # or persist it
```
