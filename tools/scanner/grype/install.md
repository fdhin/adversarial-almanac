---
tool: "grype"
build-system: "go"
upstream: "https://github.com/anchore/grype"
recipe: "blackarch"
nix-package: "grype"
aur-package: "grype"
source: "https://github.com/anchore/grype"
license: "Apache"
blackarch-pkgver: "0.33.1"
---

# Installing grype

> A vulnerability scanner for container images and filesystems.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/grype) (version `0.33.1`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S grype    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/anchore/grype/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/anchore/grype) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

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

Backlinks: [[scanner]] · [[grype]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#grype    # ad-hoc run, no install
nix profile install nixpkgs#grype   # or persist it
```
