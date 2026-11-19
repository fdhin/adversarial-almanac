---
tool: "shosubgo"
build-system: "go"
upstream: "https://github.com/incogbyte/shosubgo"
recipe: "blackarch"
source: "https://github.com/incogbyte/shosubgo"
license: "custom:unknown"
blackarch-pkgver: "V5.0.r0.gccb6dc7"
---

# Installing shosubgo

> Small tool to Grab subdomains using Shodan API.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/shosubgo) (version `V5.0.r0.gccb6dc7`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S shosubgo    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/incogbyte/shosubgo/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/incogbyte/shosubgo) (the recipe uses a versioned archive).

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

Backlinks: [[recon]] · [[shosubgo]] · [[BlackArch Tools]]

