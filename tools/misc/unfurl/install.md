---
tool: "unfurl"
build-system: "go"
upstream: "https://github.com/tomnomnom/unfurl"
recipe: "blackarch"
nix-package: "unfurl"
aur-package: "unfurl"
source: "https://github.com/tomnomnom/unfurl"
license: "MIT"
blackarch-pkgver: "33.8f10d05"
---

# Installing unfurl

> Pull out bits of URLs provided on stdin.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/unfurl) (version `33.8f10d05`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S unfurl    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/tomnomnom/unfurl/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/tomnomnom/unfurl) (the recipe uses a versioned archive).

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

Backlinks: [[misc]] · [[unfurl]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#unfurl    # ad-hoc run, no install
nix profile install nixpkgs#unfurl   # or persist it
```
