---
tool: "sj"
build-system: "go"
upstream: "https://github.com/BishopFox/sj"
recipe: "blackarch"
source: "https://github.com/BishopFox/sj"
license: "MIT"
blackarch-pkgver: "2.8.4.r0.ge1aea16"
---

# Installing sj

> A tool for auditing endpoints defined in exposed (Swagger/OpenAPI) definition files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sj) (version `2.8.4.r0.ge1aea16`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S sj    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/BishopFox/sj/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/BishopFox/sj) (the recipe uses a versioned archive).

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

Backlinks: [[webapp]] · [[sj]] · [[BlackArch Tools]]

