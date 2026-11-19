---
tool: "hakrawler"
build-system: "go"
upstream: "https://github.com/hakluke/hakrawler"
recipe: "blackarch"
nix-package: "hakrawler"
source: "https://github.com/hakluke/hakrawler"
license: "MIT"
blackarch-pkgver: "237.52a16fe"
kali-package: "hakrawler"
kali-version: "2.1-0kali1"
---

# Installing hakrawler

> Simple, fast web crawler designed for easy, quick discovery of endpoints and assets within a web application.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hakrawler) (version `237.52a16fe`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S hakrawler    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install hakrawler    # Kali package version 2.1-0kali1
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/hakluke/hakrawler/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/hakluke/hakrawler) (the recipe uses a versioned archive).

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

Backlinks: [[webapp]] · [[hakrawler]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#hakrawler    # ad-hoc run, no install
nix profile install nixpkgs#hakrawler   # or persist it
```
