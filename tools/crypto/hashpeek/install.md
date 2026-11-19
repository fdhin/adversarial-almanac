---
tool: "hashpeek"
build-system: "go"
upstream: "https://github.com/ph4mished/hashpeek"
recipe: "blackarch"
source: "https://github.com/ph4mished/hashpeek"
license: "MIT"
blackarch-pkgver: "v0.2.2.r51.g94305da"
---

# Installing hashpeek

> A fast Go-based CLI tool to identify, extract, and classify hash types from structured data/files with JSON/CSV output and Hashcat/John formatting details (a hash identifier).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hashpeek) (version `v0.2.2.r51.g94305da`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S hashpeek    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/ph4mished/hashpeek/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/ph4mished/hashpeek) (the recipe uses a versioned archive).

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

Backlinks: [[crypto]] · [[hashpeek]] · [[BlackArch Tools]]

