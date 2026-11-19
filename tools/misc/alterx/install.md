---
tool: "alterx"
build-system: "go"
upstream: "https://github.com/projectdiscovery/alterx"
recipe: "blackarch"
nix-package: "alterx"
aur-package: "alterx"
source: "https://github.com/projectdiscovery/alterx"
license: "MIT"
blackarch-pkgver: "v0.1.0.r17.g3329d23"
---

# Installing alterx

> Fast and customizable subdomain wordlist generator using DSL.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/alterx) (version `v0.1.0.r17.g3329d23`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S alterx    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/projectdiscovery/alterx/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/projectdiscovery/alterx) (the recipe uses a versioned archive).

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
    -o $pkgname ./cmd/$pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[alterx]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#alterx    # ad-hoc run, no install
nix profile install nixpkgs#alterx   # or persist it
```
