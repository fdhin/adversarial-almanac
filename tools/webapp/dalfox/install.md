---
tool: "dalfox"
build-system: "go"
upstream: "https://github.com/hahwul/dalfox"
recipe: "blackarch"
nix-package: "dalfox"
aur-package: "dalfox"
source: "https://github.com/hahwul/dalfox"
license: "MIT"
blackarch-pkgver: "v3.2.4.r76.g4d0c075"
---

# Installing dalfox

> Powerful open-source XSS scanner and utility focused on automation.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dalfox) (version `v3.2.4.r76.g4d0c075`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S dalfox    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/hahwul/dalfox/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/hahwul/dalfox) (the recipe uses a versioned archive).

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

Backlinks: [[webapp]] · [[dalfox]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dalfox    # ad-hoc run, no install
nix profile install nixpkgs#dalfox   # or persist it
```
