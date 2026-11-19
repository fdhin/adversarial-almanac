---
tool: "sourcemapper"
build-system: "go"
upstream: "https://github.com/denandz/sourcemapper"
recipe: "blackarch"
nix-package: "sourcemapper"
source: "https://github.com/denandz/sourcemapper"
license: "BSD-3-Clause"
blackarch-pkgver: "40.f739bd5"
---

# Installing sourcemapper

> Extract JavaScript source trees from Sourcemap files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sourcemapper) (version `40.f739bd5`, license BSD-3-Clause).

## Arch / BlackArch

```bash
sudo pacman -S sourcemapper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/denandz/sourcemapper/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/denandz/sourcemapper) (the recipe uses a versioned archive).

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

Backlinks: [[webapp]] · [[sourcemapper]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#sourcemapper    # ad-hoc run, no install
nix profile install nixpkgs#sourcemapper   # or persist it
```
