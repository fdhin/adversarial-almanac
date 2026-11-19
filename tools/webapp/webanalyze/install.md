---
tool: "webanalyze"
build-system: "go"
upstream: "https://github.com/rverton/webanalyze"
recipe: "blackarch"
nix-package: "webanalyze"
source: "https://github.com/rverton/webanalyze"
license: "GPL3"
blackarch-pkgver: "131.1c20ce2"
---

# Installing webanalyze

> Port of Wappalyzer (uncovers technologies used on websites) in go to automate scanning.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/webanalyze) (version `131.1c20ce2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S webanalyze    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/rverton/webanalyze/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/rverton/webanalyze) (the recipe uses a versioned archive).

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

Backlinks: [[webapp]] · [[webanalyze]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#webanalyze    # ad-hoc run, no install
nix profile install nixpkgs#webanalyze   # or persist it
```
