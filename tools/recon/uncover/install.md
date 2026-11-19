---
tool: "uncover"
build-system: "go"
upstream: "https://github.com/projectdiscovery/uncover"
recipe: "blackarch"
nix-package: "uncover"
aur-package: "uncover"
source: "https://github.com/projectdiscovery/uncover"
license: "MIT"
blackarch-pkgver: "v1.2.1.r15.g823cd7f"
---

# Installing uncover

> Discover exposed hosts on the internet using multiple search engines.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/uncover) (version `v1.2.1.r15.g823cd7f`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S uncover    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/projectdiscovery/uncover/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/projectdiscovery/uncover) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

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

Backlinks: [[recon]] · [[uncover]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#uncover    # ad-hoc run, no install
nix profile install nixpkgs#uncover   # or persist it
```
