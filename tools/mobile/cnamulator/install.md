---
tool: "cnamulator"
build-system: "go"
upstream: "https://github.com/packetassailant/cnamulator"
recipe: "blackarch"
source: "https://github.com/packetassailant/cnamulator"
license: "MIT"
blackarch-pkgver: "5.4667c68"
---

# Installing cnamulator

> A phone CNAM lookup utility using the OpenCNAM API.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cnamulator) (version `5.4667c68`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S cnamulator    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/packetassailant/cnamulator/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/packetassailant/cnamulator) (the recipe uses a versioned archive).

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

  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o $pkgname $pkgname.go
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[cnamulator]] · [[BlackArch Tools]]

