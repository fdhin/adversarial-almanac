---
tool: "aimap"
build-system: "go"
upstream: "https://github.com/Nicholas-Kloster/aimap"
recipe: "blackarch"
source: "https://github.com/Nicholas-Kloster/aimap"
license: "MIT"
blackarch-pkgver: "1.1.1"
---

# Installing aimap

> Security scanner and fingerprinter for AI/ML infrastructure. Identifies 23 service types including LLMs, vector databases, and model servers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/aimap) (version `1.1.1`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S aimap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/Nicholas-Kloster/aimap/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/Nicholas-Kloster/aimap) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `go`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  GOPATH="$srcdir" go mod download
  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o "$pkgname" .
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[ai]] · [[aimap]] · [[BlackArch Tools]]

