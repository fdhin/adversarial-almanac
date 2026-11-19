---
tool: "slurp-scanner"
build-system: "go"
upstream: "https://github.com/0xbharath/slurp"
recipe: "blackarch"
source: "https://github.com/0xbharath/slurp"
license: "GPL3"
blackarch-pkgver: "93.2dea27e"
---

# Installing slurp-scanner

> Evaluate the security of S3 buckets.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/slurp-scanner) (version `93.2dea27e`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S slurp-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/0xbharath/slurp/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/0xbharath/slurp.git
cd slurp
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

Backlinks: [[scanner]] · [[slurp-scanner]] · [[BlackArch Tools]]

