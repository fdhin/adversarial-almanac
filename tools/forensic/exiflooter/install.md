---
tool: "exiflooter"
build-system: "go"
upstream: "https://github.com/aydinnyunus/exifLooter"
recipe: "blackarch"
nix-package: "exiflooter"
source: "https://github.com/aydinnyunus/exifLooter"
license: "Apache"
blackarch-pkgver: "40.b8cc568"
---

# Installing exiflooter

> Find geolocation on all image urls and directories also integrates with OpenStreetMap.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/exiflooter) (version `40.b8cc568`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S exiflooter    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/aydinnyunus/exifLooter/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/aydinnyunus/exifLooter) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `perl-image-exiftool`

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

Backlinks: [[forensic]] · [[exiflooter]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#exiflooter    # ad-hoc run, no install
nix profile install nixpkgs#exiflooter   # or persist it
```
