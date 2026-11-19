---
tool: "metabigor"
build-system: "go"
upstream: "https://github.com/j3ssie/metabigor"
recipe: "blackarch"
nix-package: "metabigor"
source: "https://github.com/j3ssie/metabigor"
license: "MIT"
blackarch-pkgver: "89.5785d65"
---

# Installing metabigor

> Intelligence Tool but without API key.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/metabigor) (version `89.5785d65`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S metabigor    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/j3ssie/metabigor/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/j3ssie/metabigor) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `masscan`
  - `nmap`
  - `zmap`

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

Backlinks: [[recon]] · [[metabigor]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#metabigor    # ad-hoc run, no install
nix profile install nixpkgs#metabigor   # or persist it
```
