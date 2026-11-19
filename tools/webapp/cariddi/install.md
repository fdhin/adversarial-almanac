---
tool: "cariddi"
build-system: "go"
upstream: "https://github.com/edoardottt/cariddi"
recipe: "blackarch"
nix-package: "cariddi"
aur-package: "cariddi"
source: "https://github.com/edoardottt/cariddi"
license: "GPL3"
blackarch-pkgver: "v1.4.6.r16.g39aebc1"
---

# Installing cariddi

> Take a list of domains, crawl urls and scan for endpoints, secrets, api keys, file extensions, token.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cariddi) (version `v1.4.6.r16.g39aebc1`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S cariddi    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/edoardottt/cariddi/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/edoardottt/cariddi) (the recipe uses a versioned archive).

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

Backlinks: [[webapp]] · [[cariddi]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#cariddi    # ad-hoc run, no install
nix profile install nixpkgs#cariddi   # or persist it
```
