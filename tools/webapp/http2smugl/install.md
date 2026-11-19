---
tool: "http2smugl"
build-system: "go"
upstream: "https://github.com/neex/http2smugl"
recipe: "blackarch"
source: "https://github.com/neex/http2smugl"
license: "Unlicense"
blackarch-pkgver: "43.ccdf772"
---

# Installing http2smugl

> Http2Smugl - Tool to detect and exploit HTTP request smuggling in cases it can be achieved via HTTP/2 -> HTTP/1.1 conversion.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/http2smugl) (version `43.ccdf772`, license Unlicense).

## Arch / BlackArch

```bash
sudo pacman -S http2smugl    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/neex/http2smugl/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/neex/http2smugl) (the recipe uses a versioned archive).

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

Backlinks: [[webapp]] · [[http2smugl]] · [[BlackArch Tools]]

