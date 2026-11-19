---
tool: "chaos-client"
build-system: "go"
upstream: "https://github.com/projectdiscovery/chaos-client"
recipe: "blackarch"
aur-package: "chaos-client"
source: "https://github.com/projectdiscovery/chaos-client"
license: "MIT"
blackarch-pkgver: "286.b0522e0"
---

# Installing chaos-client

> Go client to communicate with Chaos dataset API.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/chaos-client) (version `286.b0522e0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S chaos-client    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/projectdiscovery/chaos-client/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/projectdiscovery/chaos-client) (the recipe uses a versioned archive).

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
    -o $pkgname ./cmd/chaos
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[chaos-client]] · [[BlackArch Tools]]

