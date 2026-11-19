---
tool: "stunner"
build-system: "go"
upstream: "https://github.com/firefart/stunner"
recipe: "blackarch"
nix-package: "stunner"
aur-package: "stunner"
source: "https://github.com/firefart/stunner"
license: "CC-BY-NC-SA-4.0"
blackarch-pkgver: "v0.5.8.r191.g3842eab"
---

# Installing stunner

> Test and exploit STUN, TURN and TURN over TCP servers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/stunner) (version `v0.5.8.r191.g3842eab`, license CC-BY-NC-SA-4.0).

## Arch / BlackArch

```bash
sudo pacman -S stunner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/firefart/stunner/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/firefart/stunner) (the recipe uses a versioned archive).

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

Backlinks: [[networking]] · [[stunner]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#stunner    # ad-hoc run, no install
nix profile install nixpkgs#stunner   # or persist it
```
