---
tool: "clair"
build-system: "go"
upstream: "https://github.com/quay/clair"
recipe: "blackarch"
nix-package: "clair"
aur-package: "clair"
source: "https://github.com/quay/clair"
license: "APACHE"
blackarch-pkgver: "2224.34177047"
provides: ["clairctl"]
---

# Installing clair

> Vulnerability Static Analysis for Containers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/clair) (version `2224.34177047`, license APACHE).

## Arch / BlackArch

```bash
sudo pacman -S clair    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/quay/clair/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/quay/clair) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `rpm-tools`

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

  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o clairctl ./cmd/clairctl
```

## Installed layout (per recipe)

- Commands: `/usr/bin/clairctl`

Backlinks: [[scanner]] · [[clair]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#clair    # ad-hoc run, no install
nix profile install nixpkgs#clair   # or persist it
```
