---
tool: "autovpn"
build-system: "go"
upstream: "https://github.com/adtac/autovpn"
recipe: "blackarch"
source: "https://github.com/adtac/autovpn"
license: "GPL3"
blackarch-pkgver: "18.28b1a87"
---

# Installing autovpn

> Easily connect to a VPN in a country of your choice.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/autovpn) (version `18.28b1a87`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S autovpn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/adtac/autovpn/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/adtac/autovpn) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `openvpn`

## Exact build steps from the recipe

```bash
cd $pkgname

  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o $pkgname "$pkgname.go"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[autovpn]] · [[BlackArch Tools]]

