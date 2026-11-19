---
tool: "kerbrute"
build-system: "go"
upstream: "https://github.com/ropnop/kerbrute"
recipe: "blackarch"
nix-package: "kerbrute"
source: "https://github.com/ropnop/kerbrute"
license: "Apache"
blackarch-pkgver: "90.9cfb81e"
---

# Installing kerbrute

> A tool to perform Kerberos pre-auth bruteforcing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kerbrute) (version `90.9cfb81e`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S kerbrute    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/ropnop/kerbrute/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/ropnop/kerbrute) (the recipe uses a versioned archive).

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

Backlinks: [[cracker]] · [[kerbrute]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#kerbrute    # ad-hoc run, no install
nix profile install nixpkgs#kerbrute   # or persist it
```
