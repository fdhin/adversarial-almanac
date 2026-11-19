---
tool: "mapcidr"
build-system: "go"
upstream: "https://github.com/projectdiscovery/mapcidr"
recipe: "blackarch"
nix-package: "mapcidr"
aur-package: "mapcidr"
source: "https://github.com/projectdiscovery/mapcidr"
license: "MIT"
blackarch-pkgver: "v1.1.97.r247.g7d653d1"
---

# Installing mapcidr

> Utility program to perform multiple operations for a given subnet/CIDR ranges.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mapcidr) (version `v1.1.97.r247.g7d653d1`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S mapcidr    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/projectdiscovery/mapcidr/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/projectdiscovery/mapcidr) (the recipe uses a versioned archive).

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

Backlinks: [[misc]] · [[mapcidr]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#mapcidr    # ad-hoc run, no install
nix profile install nixpkgs#mapcidr   # or persist it
```
