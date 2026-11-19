---
tool: "subfinder"
build-system: "go"
upstream: "https://github.com/projectdiscovery/subfinder"
recipe: "blackarch"
nix-package: "subfinder"
aur-package: "subfinder"
source: "https://github.com/projectdiscovery/subfinder"
license: "MIT"
blackarch-pkgver: "v2.14.0.r104.g5e3e2c9b"
---

# Installing subfinder

> Modular subdomain discovery tool that can discover massive amounts of valid subdomains for any target.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/subfinder) (version `v2.14.0.r104.g5e3e2c9b`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S subfinder    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/projectdiscovery/subfinder/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/projectdiscovery/subfinder) (the recipe uses a versioned archive).

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

Backlinks: [[recon]] · [[subfinder]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#subfinder    # ad-hoc run, no install
nix profile install nixpkgs#subfinder   # or persist it
```
