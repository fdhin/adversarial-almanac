---
tool: "hulk"
build-system: "go"
upstream: "https://github.com/grafov/hulk"
recipe: "blackarch"
source: "https://github.com/grafov/hulk"
license: "GPL3"
blackarch-pkgver: "27.ed2b11c"
---

# Installing hulk

> A webserver DoS tool (Http Unbearable Load King) ported to Go with some additional features.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hulk) (version `27.ed2b11c`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S hulk    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/grafov/hulk/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/grafov/hulk) (the recipe uses a versioned archive).

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

Backlinks: [[dos]] · [[hulk]] · [[BlackArch Tools]]

