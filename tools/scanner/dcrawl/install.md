---
tool: "dcrawl"
build-system: "go"
upstream: "https://github.com/kgretzky/dcrawl"
recipe: "blackarch"
source: "https://github.com/kgretzky/dcrawl"
license: "MIT"
blackarch-pkgver: "7.3273c35"
---

# Installing dcrawl

> Simple, but smart, multi-threaded web crawler for randomly gathering huge lists of unique domain names.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dcrawl) (version `7.3273c35`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S dcrawl    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/kgretzky/dcrawl/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/kgretzky/dcrawl) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

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
    -o $pkgname $pkgname.go
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[dcrawl]] · [[BlackArch Tools]]

