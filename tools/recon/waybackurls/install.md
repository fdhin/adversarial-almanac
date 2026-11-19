---
tool: "waybackurls"
build-system: "go"
upstream: "https://github.com/tomnomnom/waybackurls"
recipe: "blackarch"
nix-package: "waybackurls"
aur-package: "waybackurls"
source: "https://raw.githubusercontent.com/tomnomnom/gron/master/LICENSE"
license: "MIT"
blackarch-pkgver: "13.8d27cf3"
---

# Installing waybackurls

> Fetch all the URLs that the Wayback Machine knows about for a domain.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/waybackurls) (version `13.8d27cf3`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S waybackurls    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/tomnomnom/waybackurls/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/tomnomnom/waybackurls) (the recipe uses a versioned archive).

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

Backlinks: [[recon]] · [[waybackurls]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#waybackurls    # ad-hoc run, no install
nix profile install nixpkgs#waybackurls   # or persist it
```
