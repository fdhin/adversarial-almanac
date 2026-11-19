---
tool: "dnssearch"
build-system: "go"
upstream: "https://github.com/evilsocket/dnssearch"
recipe: "blackarch"
source: "https://github.com/evilsocket/dnssearch"
license: "GPL3"
blackarch-pkgver: "20.e4ea439"
---

# Installing dnssearch

> A subdomain enumeration tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnssearch) (version `20.e4ea439`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S dnssearch    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/evilsocket/dnssearch/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/evilsocket/dnssearch) (the recipe uses a versioned archive).

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

Backlinks: [[recon]] · [[dnssearch]] · [[BlackArch Tools]]

