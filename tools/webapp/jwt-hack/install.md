---
tool: "jwt-hack"
build-system: "go"
upstream: "https://github.com/hahwul/jwt-hack"
recipe: "blackarch"
nix-package: "jwt-hack"
aur-package: "jwt-hack"
source: "https://github.com/hahwul/jwt-hack"
license: "MIT"
blackarch-pkgver: "v2.6.0.r153.g084ed01"
---

# Installing jwt-hack

> A tool for hacking / security testing to JWT.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jwt-hack) (version `v2.6.0.r153.g084ed01`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S jwt-hack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/hahwul/jwt-hack/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/hahwul/jwt-hack) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `cargo`, `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  CFLAGS+=' -ffat-lto-objects'

  cargo clean
  cargo build --release
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[jwt-hack]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#jwt-hack    # ad-hoc run, no install
nix profile install nixpkgs#jwt-hack   # or persist it
```
