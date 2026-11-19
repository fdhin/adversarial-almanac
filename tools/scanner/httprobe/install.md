---
tool: "httprobe"
build-system: "go"
upstream: "https://github.com/tomnomnom/httprobe"
recipe: "blackarch"
nix-package: "httprobe"
aur-package: "httprobe"
source: "https://github.com/tomnomnom/httprobe"
license: "custom:unknown"
blackarch-pkgver: "24.0745f64"
---

# Installing httprobe

> Take a list of domains and probe for working HTTP and HTTPS servers

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/httprobe) (version `24.0745f64`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S httprobe    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/tomnomnom/httprobe/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/tomnomnom/httprobe) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

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

Backlinks: [[scanner]] · [[httprobe]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#httprobe    # ad-hoc run, no install
nix profile install nixpkgs#httprobe   # or persist it
```
