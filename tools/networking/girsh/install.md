---
tool: "girsh"
build-system: "go"
upstream: "https://github.com/nodauf/Girsh"
recipe: "blackarch"
nix-package: "girsh"
source: "https://github.com/nodauf/Girsh"
license: "GPL2"
blackarch-pkgver: "v0.41.r0.gfc5b44e"
---

# Installing girsh

> Automatically spawn a reverse shell fully interactive for Linux or Windows victim.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/girsh) (version `v0.41.r0.gfc5b44e`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S girsh    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/nodauf/Girsh/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/nodauf/Girsh) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $_pkgname

  GOPATH="$srcdir" go mod download
  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o $pkgname ./src
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[girsh]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#girsh    # ad-hoc run, no install
nix profile install nixpkgs#girsh   # or persist it
```
