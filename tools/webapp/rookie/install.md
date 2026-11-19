---
tool: "rookie"
build-system: "go"
upstream: "https://github.com/thewh1teagle/rookie"
recipe: "blackarch"
source: "https://github.com/thewh1teagle/rookie"
license: "MIT"
blackarch-pkgver: "v0.5.6.r2.g60ba7ae"
---

# Installing rookie

> Load cookies from your web browsers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rookie) (version `v0.5.6.r2.g60ba7ae`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S rookie    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/thewh1teagle/rookie/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/thewh1teagle/rookie) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `cargo`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel

## Exact build steps from the recipe

```bash
cd "$pkgname/cli"

  export LIBSQLITE3_SYS_USE_PKG_CONFIG=1
  cargo build --release
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[rookie]] · [[BlackArch Tools]]

