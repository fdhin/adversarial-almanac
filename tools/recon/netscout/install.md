---
tool: "netscout"
build-system: "go"
upstream: "https://github.com/caio-ishikawa/netscout"
recipe: "blackarch"
source: "https://github.com/caio-ishikawa/netscout"
license: "MIT"
blackarch-pkgver: "37.2f409df"
---

# Installing netscout

> OSINT tool that finds domains, subdomains, directories, endpoints and files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netscout) (version `37.2f409df`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S netscout    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/caio-ishikawa/netscout/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/caio-ishikawa/netscout) (the recipe uses a versioned archive).

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

Backlinks: [[recon]] · [[netscout]] · [[BlackArch Tools]]

