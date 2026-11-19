---
tool: "gf"
build-system: "go"
upstream: "https://github.com/tomnomnom/gf"
recipe: "blackarch"
nix-package: "gf"
aur-package: "gf"
source: "https://github.com/tomnomnom/gf"
license: "MIT"
blackarch-pkgver: "39.dcd4c36"
---

# Installing gf

> A wrapper around grep, to help you grep for things.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gf) (version `39.dcd4c36`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S gf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/tomnomnom/gf/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/tomnomnom/gf) (the recipe uses a versioned archive).

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

  GOPATH="$srcdir" go mod init go.mod
  GOPATH="$srcdir" go mod tidy
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

Backlinks: [[misc]] · [[gf]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#gf    # ad-hoc run, no install
nix profile install nixpkgs#gf   # or persist it
```
