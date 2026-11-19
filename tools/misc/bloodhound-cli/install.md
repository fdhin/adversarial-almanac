---
tool: "bloodhound-cli"
build-system: "go"
upstream: "https://github.com/SpecterOps/bloodhound-cli"
recipe: "blackarch"
source: "https://github.com/SpecterOps/bloodhound-cli"
license: "Apache-2.0"
blackarch-pkgver: "v0.2.1.r2.gef78c50"
---

# Installing bloodhound-cli

> Command-line interface for BloodHound v5.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bloodhound-cli) (version `v0.2.1.r2.gef78c50`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S bloodhound-cli    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/SpecterOps/bloodhound-cli/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/SpecterOps/bloodhound-cli) (the recipe uses a versioned archive).

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

Backlinks: [[misc]] · [[bloodhound-cli]] · [[BlackArch Tools]]

