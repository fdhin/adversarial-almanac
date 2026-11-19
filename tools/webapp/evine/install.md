---
tool: "evine"
build-system: "go"
upstream: "https://github.com/saeeddhqan/evine.git"
recipe: "blackarch"
aur-package: "evine"
source: "https://github.com/saeeddhqan/evine.git"
license: "GPL3"
blackarch-pkgver: "44.cfd8fa0"
---

# Installing evine

> Interactive CLI Web Crawler.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/evine) (version `44.cfd8fa0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S evine    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/saeeddhqan/evine.git/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/saeeddhqan/evine.git) (the recipe uses a versioned archive).

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

Backlinks: [[webapp]] · [[evine]] · [[BlackArch Tools]]

