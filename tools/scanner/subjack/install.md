---
tool: "subjack"
build-system: "go"
upstream: "https://github.com/haccer/subjack"
recipe: "blackarch"
nix-package: "subjack"
source: "https://github.com/haccer/subjack"
license: "Apache"
blackarch-pkgver: "182.49c51e5"
---

# Installing subjack

> Subdomain Takeover tool written in Go.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/subjack) (version `182.49c51e5`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S subjack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/haccer/subjack/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/haccer/subjack) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $pkgname

  GOPATH="$srcdir" go mod init go.mod
  GOPATH="$srcdir" go mod tidy
  GOPATH="$srcdir" go mod download
  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o "$pkgname-bin" .
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[subjack]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#subjack    # ad-hoc run, no install
nix profile install nixpkgs#subjack   # or persist it
```
