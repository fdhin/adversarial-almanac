---
tool: "cero"
build-system: "go"
upstream: "https://github.com/glebarez/cero"
recipe: "blackarch"
nix-package: "cero"
source: "https://github.com/glebarez/cero"
license: "custom:unknown"
blackarch-pkgver: "v1.3.0.r19.gb73125b"
---

# Installing cero

> Scrape domain names from SSL certificates of arbitrary hosts.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cero) (version `v1.3.0.r19.gb73125b`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S cero    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/glebarez/cero/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/glebarez/cero) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

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

Backlinks: [[scanner]] · [[cero]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#cero    # ad-hoc run, no install
nix profile install nixpkgs#cero   # or persist it
```
