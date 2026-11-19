---
tool: "katana-pd"
build-system: "go"
upstream: "https://github.com/projectdiscovery/katana"
recipe: "blackarch"
source: "https://github.com/projectdiscovery/katana"
license: "MIT"
blackarch-pkgver: "v1.8.0.r100.ge1a9ba74"
---

# Installing katana-pd

> Crawling and spidering framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/katana-pd) (version `v1.8.0.r100.ge1a9ba74`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S katana-pd    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/projectdiscovery/katana/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/projectdiscovery/katana) (the recipe uses a versioned archive).

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
    -o $_pkgname ./cmd/$_pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[katana-pd]] · [[BlackArch Tools]]

