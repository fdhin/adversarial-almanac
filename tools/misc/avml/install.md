---
tool: "avml"
build-system: "go"
upstream: "https://github.com/microsoft/avml"
recipe: "blackarch"
nix-package: "avml"
aur-package: "avml"
source: "https://github.com/microsoft/avml"
license: "MIT"
blackarch-pkgver: "v0.21.0.r5.gb007a3c"
---

# Installing avml

> A portable volatile memory acquisition tool for Linux.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/avml) (version `v0.21.0.r5.gb007a3c`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S avml    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/microsoft/avml/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/microsoft/avml) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `cargo`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `musl`
  - `rust-musl`

## Exact build steps from the recipe

```bash
cd $pkgname

  export RUSTUP_TOOLCHAIN=stable
  export CARGO_TARGET_DIR=target
  cargo build --release --all-features
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[avml]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#avml    # ad-hoc run, no install
nix profile install nixpkgs#avml   # or persist it
```
