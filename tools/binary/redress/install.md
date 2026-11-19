---
tool: "redress"
build-system: "go"
upstream: "https://github.com/goretk/redress"
recipe: "blackarch"
aur-package: "redress"
source: "https://github.com/goretk/redress"
license: "AGPL"
blackarch-pkgver: "v1.2.2.r0.gfe38d96"
---

# Installing redress

> A tool for analyzing stripped Go binaries.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/redress) (version `v1.2.2.r0.gfe38d96`, license AGPL).

## Arch / BlackArch

```bash
sudo pacman -S redress    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/goretk/redress/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/goretk/redress) (the recipe uses a versioned archive).

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
    -ldflags="-s -w -X main.redressVersion=$pkgver" \
    -o $pkgname .
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[redress]] · [[BlackArch Tools]]

