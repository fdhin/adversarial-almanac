---
tool: "raven"
build-system: "go"
upstream: "https://github.com/0x09AL/raven"
recipe: "blackarch"
aur-package: "raven"
source: "https://github.com/0x09AL/raven"
license: "GPL3"
blackarch-pkgver: "33.8646a58"
---

# Installing raven

> A Linkedin information gathering tool that can be used by pentesters to gather information about an organization employees using Linkedin.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/raven) (version `33.8646a58`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S raven    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/0x09AL/raven/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/0x09AL/raven) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $pkgname

  GOROOT="$srcdir/go" GOPATH="$srcdir" go mod init go.mod
  GOROOT="$srcdir/go" GOPATH="$srcdir" go mod tidy
  GOROOT="$srcdir/go" GOPATH="$srcdir" go mod download
  GOROOT="$srcdir/go" GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o $pkgname .
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[raven]] · [[BlackArch Tools]]

