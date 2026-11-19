---
tool: "assetfinder"
build-system: "go"
upstream: "https://github.com/tomnomnom/assetfinder"
recipe: "blackarch"
nix-package: "assetfinder"
aur-package: "assetfinder"
source: "https://github.com/tomnomnom/assetfinder"
license: "MIT"
blackarch-pkgver: "v0.1.1.r0.g4e95d87"
kali-package: "assetfinder"
kali-version: "0.1.1-1+b12"
---

# Installing assetfinder

> Find domains and subdomains potentially related to a given domain.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/assetfinder) (version `v0.1.1.r0.g4e95d87`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S assetfinder    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install assetfinder    # Kali package version 0.1.1-1+b12
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/tomnomnom/assetfinder/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/tomnomnom/assetfinder) (the recipe uses a versioned archive).

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

Backlinks: [[scanner]] · [[assetfinder]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#assetfinder    # ad-hoc run, no install
nix profile install nixpkgs#assetfinder   # or persist it
```
