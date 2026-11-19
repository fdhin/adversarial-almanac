---
tool: "wdict"
build-system: "go"
upstream: "https://github.com/pyqlsa/wdict"
recipe: "blackarch"
source: "https://github.com/pyqlsa/wdict"
license: "MIT"
blackarch-pkgver: "v0.1.23.r0.g3b7f753"
---

# Installing wdict

> Create dictionaries by scraping webpages or crawling local files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wdict) (version `v0.1.23.r0.g3b7f753`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S wdict    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/pyqlsa/wdict/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/pyqlsa/wdict) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `cargo`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd $pkgname

  cargo build --release --locked
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wordlist]] · [[wdict]] · [[BlackArch Tools]]

