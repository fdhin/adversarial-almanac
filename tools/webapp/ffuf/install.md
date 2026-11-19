---
tool: "ffuf"
build-system: "go"
upstream: "https://github.com/ffuf/ffuf"
recipe: "blackarch"
nix-package: "ffuf"
aur-package: "ffuf"
source: "https://github.com/ffuf/ffuf"
license: "MIT"
blackarch-pkgver: "v2.3.0.r7.g8d65e03"
---

# Installing ffuf

> Fast web fuzzer written in Go.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ffuf) (version `v2.3.0.r7.g8d65e03`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S ffuf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/ffuf/ffuf/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/ffuf/ffuf) (the recipe uses a versioned archive).

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

Backlinks: [[webapp]] · [[ffuf]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ffuf    # ad-hoc run, no install
nix profile install nixpkgs#ffuf   # or persist it
```
