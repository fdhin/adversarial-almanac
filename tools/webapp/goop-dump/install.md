---
tool: "goop-dump"
build-system: "go"
upstream: "https://github.com/nyancrimew/goop"
recipe: "blackarch"
source: "https://github.com/nyancrimew/goop"
license: "MIT"
blackarch-pkgver: "75.d465a6d"
---

# Installing goop-dump

> Tool to dump a git repository from a website, focused on as-complete-as-possible dumps and handling weird edge-cases.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/goop-dump) (version `75.d465a6d`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S goop-dump    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/nyancrimew/goop/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/nyancrimew/goop) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $_pkgname

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

Backlinks: [[webapp]] · [[goop-dump]] · [[BlackArch Tools]]

