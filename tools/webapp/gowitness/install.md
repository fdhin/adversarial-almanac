---
tool: "gowitness"
build-system: "go"
upstream: "https://github.com/sensepost/gowitness"
recipe: "blackarch"
nix-package: "gowitness"
aur-package: "gowitness"
source: "https://github.com/sensepost/gowitness"
license: "GPL-3.0-only"
blackarch-pkgver: "3.2.2.r0.g601c843"
---

# Installing gowitness

> A golang, web screenshot utility using Chrome Headless.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gowitness) (version `3.2.2.r0.g601c843`, license GPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S gowitness    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/sensepost/gowitness/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/sensepost/gowitness) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `chromium`

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

Backlinks: [[webapp]] · [[gowitness]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#gowitness    # ad-hoc run, no install
nix profile install nixpkgs#gowitness   # or persist it
```
