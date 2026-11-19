---
tool: "passdetective"
build-system: "go"
upstream: "https://github.com/aydinnyunus/PassDetective"
recipe: "blackarch"
nix-package: "passdetective"
source: "https://github.com/aydinnyunus/PassDetective"
license: "Apache-2.0"
blackarch-pkgver: "1.0.6.r4.g458f12e"
---

# Installing passdetective

> Scans shell command history to detect mistakenly written passwords, API keys, and secrets.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/passdetective) (version `1.0.6.r4.g458f12e`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S passdetective    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/aydinnyunus/PassDetective/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/aydinnyunus/PassDetective) (the recipe uses a versioned archive).

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

Backlinks: [[misc]] · [[passdetective]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#passdetective    # ad-hoc run, no install
nix profile install nixpkgs#passdetective   # or persist it
```
