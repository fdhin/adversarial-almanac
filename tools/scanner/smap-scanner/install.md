---
tool: "smap-scanner"
build-system: "go"
upstream: "https://github.com/s0md3v/Smap"
recipe: "blackarch"
source: "https://github.com/s0md3v/Smap"
license: "AGPL3"
blackarch-pkgver: "0.1.12.r5.gfcf43c1"
---

# Installing smap-scanner

> Passive port scanner built with shodan free API.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/smap-scanner) (version `0.1.12.r5.gfcf43c1`, license AGPL3).

## Arch / BlackArch

```bash
sudo pacman -S smap-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/s0md3v/Smap/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/s0md3v/Smap) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $_pkgname/cmd/$_pkgname

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

Backlinks: [[scanner]] · [[smap-scanner]] · [[BlackArch Tools]]

