---
tool: "oniongrok"
build-system: "go"
upstream: "https://github.com/cmars/oniongrok"
recipe: "blackarch"
source: "https://github.com/cmars/oniongrok"
license: "MIT"
blackarch-pkgver: "v1.1.11.r0.gcbae9e7"
---

# Installing oniongrok

> Onion addresses for anything.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/oniongrok) (version `v1.1.11.r0.gcbae9e7`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S oniongrok    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/cmars/oniongrok/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/cmars/oniongrok) (the recipe uses a versioned archive).

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

Backlinks: [[tunnel]] · [[oniongrok]] · [[BlackArch Tools]]

