---
tool: "feroxbuster"
build-system: "go"
upstream: "https://github.com/epi052/feroxbuster"
recipe: "blackarch"
nix-package: "feroxbuster"
aur-package: "feroxbuster"
source: "https://github.com/epi052/feroxbuster"
license: "MIT"
blackarch-pkgver: "v2.13.1.r27.gdba16dd"
---

# Installing feroxbuster

> A fast, simple, recursive content discovery tool written in Rust.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/feroxbuster) (version `v2.13.1.r27.gdba16dd`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S feroxbuster    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/epi052/feroxbuster/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/epi052/feroxbuster) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `cargo`, `git`, `pkgconf`
- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  # disable lto causing build failing
  # https://archlinux.org/todo/lto-fat-objects/
  # https://github.com/epi052/feroxbuster/blob/b44c52f0ea1fb5a3ac897ccd349e840c9d3440e9/Cargo.toml#L78
  CFLAGS+=" -ffat-lto-objects"

  cargo build --release --locked
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[feroxbuster]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#feroxbuster    # ad-hoc run, no install
nix profile install nixpkgs#feroxbuster   # or persist it
```
