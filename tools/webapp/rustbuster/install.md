---
tool: "rustbuster"
build-system: "go"
upstream: "https://github.com/phra/rustbuster"
recipe: "blackarch"
aur-package: "rustbuster"
source: "https://github.com/phra/rustbuster"
license: "GPL3"
blackarch-pkgver: "302.4a243d4"
---

# Installing rustbuster

> DirBuster for Rust.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rustbuster) (version `302.4a243d4`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S rustbuster    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/phra/rustbuster/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/phra/rustbuster) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `cargo`, `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  cargo build --release
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[rustbuster]] · [[BlackArch Tools]]

