---
tool: "nextnet"
build-system: "go"
upstream: "https://github.com/hdm/nextnet"
recipe: "blackarch"
source: "https://github.com/hdm/nextnet"
license: "BSD"
blackarch-pkgver: "4.2c66df3"
---

# Installing nextnet

> Pivot point discovery tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nextnet) (version `4.2c66df3`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S nextnet    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/hdm/nextnet/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/hdm/nextnet) (the recipe uses a versioned archive).

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

Backlinks: [[networking]] · [[nextnet]] · [[BlackArch Tools]]

