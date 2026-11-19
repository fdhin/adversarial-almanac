---
tool: "zgrab"
build-system: "go"
upstream: "https://github.com/zmap/zgrab"
recipe: "blackarch"
source: "https://github.com/zmap/zgrab"
license: "Apache"
blackarch-pkgver: "804.59a517f"
---

# Installing zgrab

> Grab banners (optionally over TLS).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/zgrab) (version `804.59a517f`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S zgrab    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/zmap/zgrab/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/zmap/zgrab) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $pkgname

  GOPATH="$srcdir" go mod init go.mod
  GOPATH="$srcdir" go mod edit -replace github.com/zmap/zgrab@latest=github.com/zmap/zgrab@031475eccfdeae85d3639626cfe40de08880c3c4
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

Backlinks: [[recon]] · [[zgrab]] · [[BlackArch Tools]]

