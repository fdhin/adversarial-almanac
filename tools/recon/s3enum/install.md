---
tool: "s3enum"
build-system: "go"
upstream: "https://github.com/koenrh/s3enum"
recipe: "blackarch"
source: "https://github.com/koenrh/s3enum"
license: "ISC"
blackarch-pkgver: "v1.1.0.r6.ge9b9bb8"
---

# Installing s3enum

> Amazon S3 bucket enumeration.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/s3enum) (version `v1.1.0.r6.ge9b9bb8`, license ISC).

## Arch / BlackArch

```bash
sudo pacman -S s3enum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/koenrh/s3enum/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/koenrh/s3enum) (the recipe uses a versioned archive).

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

Backlinks: [[recon]] · [[s3enum]] · [[BlackArch Tools]]

