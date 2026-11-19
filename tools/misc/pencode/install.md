---
tool: "pencode"
build-system: "go"
upstream: "https://github.com/ffuf/pencode"
recipe: "blackarch"
source: "https://github.com/ffuf/pencode"
license: "MIT"
blackarch-pkgver: "39.07e33ef"
---

# Installing pencode

> Complex payload encoder.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pencode) (version `39.07e33ef`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pencode    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/ffuf/pencode/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/ffuf/pencode) (the recipe uses a versioned archive).

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
    -o $pkgname ./cmd/$pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[pencode]] · [[BlackArch Tools]]

