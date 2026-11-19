---
tool: "brutespray"
build-system: "go"
upstream: "https://github.com/x90skysn3k/brutespray"
recipe: "blackarch"
nix-package: "brutespray"
source: "https://github.com/x90skysn3k/brutespray"
license: "MIT"
blackarch-pkgver: "908.d4e361b"
---

# Installing brutespray

> Brute-Forcing from Nmap output - Automatically attempts default creds on found services.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/brutespray) (version `908.d4e361b`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S brutespray    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/x90skysn3k/brutespray/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/x90skysn3k/brutespray) (the recipe uses a versioned archive).

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

Backlinks: [[automation]] · [[brutespray]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#brutespray    # ad-hoc run, no install
nix profile install nixpkgs#brutespray   # or persist it
```
