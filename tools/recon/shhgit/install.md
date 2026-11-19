---
tool: "shhgit"
build-system: "go"
upstream: "https://github.com/eth0izzle/shhgit"
recipe: "blackarch"
aur-package: "shhgit"
source: "https://github.com/eth0izzle/shhgit"
license: "MIT"
blackarch-pkgver: "67.bac0c7d"
---

# Installing shhgit

> Find committed secrets and sensitive files across GitHub, Gists, GitLab and BitBucket or your local repositories in real time.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/shhgit) (version `67.bac0c7d`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S shhgit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/eth0izzle/shhgit/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/eth0izzle/shhgit) (the recipe uses a versioned archive).

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

Backlinks: [[recon]] · [[shhgit]] · [[BlackArch Tools]]

