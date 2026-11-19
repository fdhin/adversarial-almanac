---
tool: "rbac-lookup"
build-system: "go"
upstream: "https://github.com/FairwindsOps/rbac-lookup"
recipe: "blackarch"
source: "https://github.com/FairwindsOps/rbac-lookup"
license: "Apache"
blackarch-pkgver: "v0.10.3.r0.g46385c5"
---

# Installing rbac-lookup

> A CLI that allows you to easily find Kubernetes roles and cluster roles bound to any user.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rbac-lookup) (version `v0.10.3.r0.g46385c5`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S rbac-lookup    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/FairwindsOps/rbac-lookup/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/FairwindsOps/rbac-lookup) (the recipe uses a versioned archive).

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

Backlinks: [[scanner]] · [[rbac-lookup]] · [[BlackArch Tools]]

