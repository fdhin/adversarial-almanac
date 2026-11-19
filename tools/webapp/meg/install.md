---
tool: "meg"
build-system: "go"
upstream: "https://github.com/tomnomnom/meg"
recipe: "blackarch"
nix-package: "meg"
aur-package: "meg"
source: "https://github.com/tomnomnom/meg"
license: "MIT"
blackarch-pkgver: "89.5cc1d34"
---

# Installing meg

> Fetch many paths for many hosts - without killing the hosts.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/meg) (version `89.5cc1d34`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S meg    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/tomnomnom/meg/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/tomnomnom/meg) (the recipe uses a versioned archive).

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

Backlinks: [[webapp]] · [[meg]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#meg    # ad-hoc run, no install
nix profile install nixpkgs#meg   # or persist it
```
