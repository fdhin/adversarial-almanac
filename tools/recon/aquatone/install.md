---
tool: "aquatone"
build-system: "go"
upstream: "https://github.com/shelld3v/aquatone"
recipe: "blackarch"
aur-package: "aquatone"
source: "https://github.com/shelld3v/aquatone"
license: "MIT"
blackarch-pkgver: "v1.9.1.shelld3v.r14.g30fbf6e"
---

# Installing aquatone

> A Tool for Domain Flyovers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/aquatone) (version `v1.9.1.shelld3v.r14.g30fbf6e`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S aquatone    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/shelld3v/aquatone/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/shelld3v/aquatone) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `chromium`
  - `go-pie`

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

Backlinks: [[recon]] · [[aquatone]] · [[BlackArch Tools]]

