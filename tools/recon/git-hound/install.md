---
tool: "git-hound"
build-system: "go"
upstream: "https://github.com/tillson/git-hound"
recipe: "blackarch"
nix-package: "git-hound"
source: "https://github.com/tillson/git-hound"
license: "MIT"
blackarch-pkgver: "258.15701b7"
---

# Installing git-hound

> Pinpoints exposed API keys on GitHub. A batch-catching, pattern-matching, patch-attacking secret snatcher.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/git-hound) (version `258.15701b7`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S git-hound    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/tillson/git-hound/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/tillson/git-hound) (the recipe uses a versioned archive).

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

Backlinks: [[recon]] · [[git-hound]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#git-hound    # ad-hoc run, no install
nix profile install nixpkgs#git-hound   # or persist it
```
