---
tool: "dontgo403"
build-system: "go"
upstream: "https://github.com/devploit/dontgo403"
recipe: "blackarch"
source: "https://github.com/devploit/dontgo403"
license: "Apache"
blackarch-pkgver: "v2.0.1.r3.g57e0a39"
---

# Installing dontgo403

> Tool to bypass 40X response codes..

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dontgo403) (version `v2.0.1.r3.g57e0a39`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S dontgo403    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/devploit/dontgo403/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/devploit/dontgo403) (the recipe uses a versioned archive).

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

Backlinks: [[webapp]] · [[dontgo403]] · [[BlackArch Tools]]

