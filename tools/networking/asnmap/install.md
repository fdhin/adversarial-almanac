---
tool: "asnmap"
build-system: "go"
upstream: "https://github.com/projectdiscovery/asnmap"
recipe: "blackarch"
nix-package: "asnmap"
aur-package: "asnmap"
source: "https://github.com/projectdiscovery/asnmap"
license: "MIT"
blackarch-pkgver: "v1.2.0.r0.gab84a74"
---

# Installing asnmap

> Map organization network ranges using ASN information.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/asnmap) (version `v1.2.0.r0.gab84a74`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S asnmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/projectdiscovery/asnmap/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/projectdiscovery/asnmap) (the recipe uses a versioned archive).

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
    -o $pkgname ./cmd/$pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[asnmap]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#asnmap    # ad-hoc run, no install
nix profile install nixpkgs#asnmap   # or persist it
```
