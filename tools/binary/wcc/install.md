---
tool: "wcc"
build-system: "rust"
upstream: "https://github.com/endrazine/wcc"
recipe: "blackarch"
nix-package: "wcc"
aur-package: "wcc"
source: "https://github.com/endrazine/wcc"
license: "MIT, BSD-2-Clause, custom:unknown"
blackarch-pkgver: "v0.0.13.r1.g0db1624"
---

# Installing wcc

> The Witchcraft Compiler Collection.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wcc) (version `v0.0.13.r1.g0db1624`, license MIT, BSD-2-Clause, custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S wcc    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary:** check [upstream releases](https://github.com/endrazine/wcc/releases).

**B. From source (needs `cargo`):**

**Source:** download the release from the [upstream site](https://github.com/endrazine/wcc) (the recipe uses a versioned archive).

```bash
cargo build --release
# binary in target/release/
```

## Dependencies (from recipe)

- **Build tools**: `cargo`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `capstone`
  - `clang`
  - `gsl`
  - `libelf`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[binary]] · [[wcc]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#wcc    # ad-hoc run, no install
nix profile install nixpkgs#wcc   # or persist it
```
