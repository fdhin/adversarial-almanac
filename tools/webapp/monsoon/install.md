---
tool: "monsoon"
build-system: "go"
upstream: "https://github.com/RedTeamPentesting/monsoon"
recipe: "blackarch"
nix-package: "monsoon"
aur-package: "monsoon"
source: "https://github.com/RedTeamPentesting/monsoon"
license: "MIT"
blackarch-pkgver: "261.f4f9852"
---

# Installing monsoon

> A fast HTTP enumerator that allows you to execute a large number of HTTP requests.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/monsoon) (version `261.f4f9852`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S monsoon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/RedTeamPentesting/monsoon/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/RedTeamPentesting/monsoon) (the recipe uses a versioned archive).

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
    -o $pkgname .
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[monsoon]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#monsoon    # ad-hoc run, no install
nix profile install nixpkgs#monsoon   # or persist it
```
