---
tool: "pwfuzz-rs"
build-system: "go"
upstream: "https://github.com/mttaggart/pwfuzz-rs"
recipe: "blackarch"
source: "https://github.com/mttaggart/pwfuzz-rs"
license: "MIT"
blackarch-pkgver: "v0.2.0.r1.gac56f42"
---

# Installing pwfuzz-rs

> Rust-based password mutator for brute force attacks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pwfuzz-rs) (version `v0.2.0.r1.gac56f42`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pwfuzz-rs    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/mttaggart/pwfuzz-rs/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/mttaggart/pwfuzz-rs) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `cargo`, `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  cargo build --release --locked
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[pwfuzz-rs]] · [[BlackArch Tools]]

