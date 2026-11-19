---
tool: "interlock-git"
ebuild-package: "interlock-git"
build-system: "go"
upstream: "https://github.com/f-secure-foundry/interlock"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/interlock-git"
version: "20200902.r415"
license: "GPL3"
---

# Installing interlock-git

> File encryption front-end

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/interlock-git) (version `20200902.r415`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S interlock-git
```

The same PKGBUILD is usually on the AUR (`paru -S interlock-git`).

## Any other Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/f-secure-foundry/interlock/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/f-secure-foundry/interlock) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `cryptsetup`
  - `go-pie`
  - `inetutils`
  - `lvm2`
  - `mercurial`
  - `sudo`

## Exact build steps from the recipe

```bash
cd "${srcdir}/${pkgname}"
  export GOPATH="${srcdir}"
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[crypto]] · [[interlock-git]] · [[ArchStrike Gap Analysis]]
