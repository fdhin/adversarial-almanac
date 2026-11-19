---
tool: "ipv666"
build-system: "go"
upstream: "https://github.com/lavalamp-/ipv666"
recipe: "blackarch"
aur-package: "ipv666"
source: "https://github.com/lavalamp-/ipv666"
license: "BSD"
blackarch-pkgver: "182.ad45ae8"
---

# Installing ipv666

> Golang IPv6 address enumeration.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ipv666) (version `182.ad45ae8`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S ipv666    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/lavalamp-/ipv666/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/lavalamp-/ipv666) (the recipe uses a versioned archive).

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
    -o $pkgname-bin ./$pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[ipv666]] · [[BlackArch Tools]]

