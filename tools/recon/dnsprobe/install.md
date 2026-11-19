---
tool: "dnsprobe"
build-system: "go"
upstream: "https://github.com/projectdiscovery/dnsprobe"
recipe: "blackarch"
aur-package: "dnsprobe"
source: "https://github.com/projectdiscovery/dnsprobe"
license: "MIT"
blackarch-pkgver: "56.7120008"
---

# Installing dnsprobe

> Allows you to perform multiple dns queries of your choice with a list of user supplied resolvers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnsprobe) (version `56.7120008`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S dnsprobe    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/projectdiscovery/dnsprobe/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/projectdiscovery/dnsprobe) (the recipe uses a versioned archive).

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

Backlinks: [[recon]] · [[dnsprobe]] · [[BlackArch Tools]]

