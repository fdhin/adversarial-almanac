---
tool: "pius-pi"
build-system: "go"
upstream: "https://github.com/praetorian-inc/pius"
recipe: "blackarch"
source: "https://github.com/praetorian-inc/pius"
license: "Apache-2.0"
blackarch-pkgver: "1.3.18.r0.gff1dbcb"
---

# Installing pius-pi

> Organizational asset discovery tool with 20+ plugins covering certificate transparency, passive DNS, and all 5 Regional Internet Registries.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pius-pi) (version `1.3.18.r0.gff1dbcb`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S pius-pi    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/praetorian-inc/pius/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/praetorian-inc/pius) (the recipe uses a versioned archive).

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
    -o $pkgname "./cmd/$_pkgname"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[pius-pi]] · [[BlackArch Tools]]

