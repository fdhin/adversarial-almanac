---
tool: "rustpad"
build-system: "go"
upstream: "https://github.com/Kibouo/rustpad"
recipe: "blackarch"
aur-package: "rustpad"
source: "https://github.com/Kibouo/rustpad"
license: "GPL3"
blackarch-pkgver: "v1.8.1.r1.g11ce343"
---

# Installing rustpad

> Multi-threaded Padding Oracle attacks against any service.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rustpad) (version `v1.8.1.r1.g11ce343`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S rustpad    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/Kibouo/rustpad/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/Kibouo/rustpad) (the recipe uses a versioned archive).

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

Backlinks: [[crypto]] · [[rustpad]] · [[BlackArch Tools]]

