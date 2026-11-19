---
tool: "trufflehog"
build-system: "go"
upstream: "https://github.com/dxa4481/truffleHog"
recipe: "blackarch"
nix-package: "trufflehog"
aur-package: "trufflehog"
source: "https://github.com/dxa4481/truffleHog"
license: "GPL2"
blackarch-pkgver: "v3.99.7.r9.g421026a65"
---

# Installing trufflehog

> Searches through git repositories for high entropy strings, digging deep into commit history.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/trufflehog) (version `v3.99.7.r9.g421026a65`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S trufflehog    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/dxa4481/truffleHog/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/dxa4481/truffleHog) (the recipe uses a versioned archive).

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

Backlinks: [[code-audit]] · [[trufflehog]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#trufflehog    # ad-hoc run, no install
nix profile install nixpkgs#trufflehog   # or persist it
```
