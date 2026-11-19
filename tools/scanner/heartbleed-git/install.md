---
tool: "heartbleed-git"
ebuild-package: "heartbleed-git"
build-system: "go"
upstream: "https://$_url"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/heartbleed-git"
version: "119.v0.2.0.r48.g4a3332c"
license: "MIT"
---

# Installing heartbleed-git

> Test whether a host is vulnerable to the Heartbleed attack

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/heartbleed-git) (version `119.v0.2.0.r48.g4a3332c`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S heartbleed-git
```

The same PKGBUILD is usually on the AUR (`paru -S heartbleed-git`).

## Any other Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://$_url/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone git://github.com/FiloSottile/Heartbleed.git
cd Heartbleed
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd "$srcdir"
  # makepkg doesn't support extracting sources to a specific directory
  mkdir -p src/$_url && rm -rf src/$_url && mv $_pkgname src/$_url && cd src/$_url
  GOPATH="$srcdir" go get -d -t github.com/FiloSottile/Heartbleed
```

## Installed layout (per recipe)

- Commands: `/usr/bin/heartbleed`

Backlinks: [[scanner]] · [[heartbleed-git]] · [[ArchStrike Gap Analysis]]
