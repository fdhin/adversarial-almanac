---
tool: "turner"
build-system: "go"
upstream: "https://github.com/staaldraad/turner"
recipe: "blackarch"
source: "https://github.com/staaldraad/turner"
license: "MIT"
blackarch-pkgver: "32.fad9423"
---

# Installing turner

> Tunnels HTTP over a permissive/open TURN server; supports HTTP and SOCKS5 proxy.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/turner) (version `32.fad9423`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S turner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/staaldraad/turner/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/staaldraad/turner) (the recipe uses a versioned archive).

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

Backlinks: [[networking]] · [[turner]] · [[BlackArch Tools]]

