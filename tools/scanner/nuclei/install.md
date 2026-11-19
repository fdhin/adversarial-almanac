---
tool: "nuclei"
build-system: "go"
upstream: "https://github.com/projectdiscovery/nuclei"
recipe: "blackarch"
nix-package: "nuclei"
aur-package: "nuclei"
source: "https://github.com/projectdiscovery/nuclei"
license: "MIT"
blackarch-pkgver: "v3.11.1.r579.gdf57ae6"
---

# Installing nuclei

> A fast tool for configurable targeted scanning based on templates offering massive extensibility and ease of use.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nuclei) (version `v3.11.1.r579.gdf57ae6`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S nuclei    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/projectdiscovery/nuclei/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/projectdiscovery/nuclei) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `nuclei-templates`

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
    -o $pkgname "./cmd/$pkgname"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[nuclei]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#nuclei    # ad-hoc run, no install
nix profile install nixpkgs#nuclei   # or persist it
```
