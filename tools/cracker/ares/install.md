---
tool: "ares"
build-system: "go"
upstream: "https://github.com/bee-san/ares"
recipe: "blackarch"
nix-package: "ares"
source: "https://github.com/bee-san/ares"
license: "MIT"
blackarch-pkgver: "0.11.0.r560.g7c7481f"
---

# Installing ares

> Automated decoding of encrypted text without knowing the key or ciphers used.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ares) (version `0.11.0.r560.g7c7481f`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S ares    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/bee-san/Ares/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/bee-san/Ares) (the recipe uses a versioned archive).

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

Backlinks: [[cracker]] · [[ares]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ares    # ad-hoc run, no install
nix profile install nixpkgs#ares   # or persist it
```
