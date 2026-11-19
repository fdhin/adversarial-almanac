---
tool: "dns-reverse-proxy"
build-system: "go"
upstream: "https://github.com/StalkR/dns-reverse-proxy"
recipe: "blackarch"
source: "https://github.com/StalkR/dns-reverse-proxy"
license: "Apache"
blackarch-pkgver: "45.e3d33b9"
---

# Installing dns-reverse-proxy

> A reverse DNS proxy written in Go.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dns-reverse-proxy) (version `45.e3d33b9`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S dns-reverse-proxy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/StalkR/dns-reverse-proxy/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/StalkR/dns-reverse-proxy) (the recipe uses a versioned archive).

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

Backlinks: [[proxy]] · [[dns-reverse-proxy]] · [[BlackArch Tools]]

