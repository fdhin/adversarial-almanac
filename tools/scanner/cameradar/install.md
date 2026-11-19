---
tool: "cameradar"
build-system: "go"
upstream: "https://github.com/Ullaakut/cameradar"
recipe: "blackarch"
nix-package: "cameradar"
source: "https://github.com/Ullaakut/cameradar"
license: "MIT"
blackarch-pkgver: "287.0ee490c"
---

# Installing cameradar

> Hacks its way into RTSP videosurveillance cameras.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cameradar) (version `287.0ee490c`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S cameradar    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/Ullaakut/cameradar/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/Ullaakut/cameradar) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `nmap`

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
    -o $pkgname ./cmd/cameradar
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[cameradar]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#cameradar    # ad-hoc run, no install
nix profile install nixpkgs#cameradar   # or persist it
```
