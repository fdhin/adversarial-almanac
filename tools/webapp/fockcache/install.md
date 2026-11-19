---
tool: "fockcache"
build-system: "go"
upstream: "https://github.com/tismayil/fockcache"
recipe: "blackarch"
source: "https://github.com/tismayil/fockcache"
license: "GPL2"
blackarch-pkgver: "10.3e7efa9"
---

# Installing fockcache

> Tool to make cache poisoning by trying X-Forwarded-Host and X-Forwarded-Scheme headers on web pages.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fockcache) (version `10.3e7efa9`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S fockcache    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/tismayil/fockcache/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/tismayil/fockcache) (the recipe uses a versioned archive).

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
    -o $pkgname .
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[fockcache]] · [[BlackArch Tools]]

