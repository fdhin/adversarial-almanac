---
tool: "urx"
build-system: "go"
upstream: "https://github.com/hahwul/urx"
recipe: "blackarch"
nix-package: "urx"
aur-package: "urx"
source: "https://github.com/hahwul/urx"
license: "MIT"
blackarch-pkgver: "0.11.0.r132.g3caebea"
---

# Installing urx

> Extracts URLs from OSINT Archives for Security Insights.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/urx) (version `0.11.0.r132.g3caebea`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S urx    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/hahwul/urx/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/hahwul/urx) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `cargo`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
CFLAGS+=' -ffat-lto-objects'
  cd $pkgname

  cargo build --release --locked
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[urx]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#urx    # ad-hoc run, no install
nix profile install nixpkgs#urx   # or persist it
```
