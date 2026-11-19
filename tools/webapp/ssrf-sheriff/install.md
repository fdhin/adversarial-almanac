---
tool: "ssrf-sheriff"
build-system: "go"
upstream: "https://github.com/teknogeek/ssrf-sheriff"
recipe: "blackarch"
source: "https://github.com/teknogeek/ssrf-sheriff"
license: "MIT"
blackarch-pkgver: "8.02cd31d"
---

# Installing ssrf-sheriff

> A simple SSRF-testing sheriff written in Go.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ssrf-sheriff) (version `8.02cd31d`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S ssrf-sheriff    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/teknogeek/ssrf-sheriff/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/teknogeek/ssrf-sheriff) (the recipe uses a versioned archive).

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

Backlinks: [[webapp]] · [[ssrf-sheriff]] · [[BlackArch Tools]]

