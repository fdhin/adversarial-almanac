---
tool: "chainsaw"
build-system: "go"
upstream: "https://github.com/countercept/chainsaw"
recipe: "blackarch"
nix-package: "chainsaw"
aur-package: "chainsaw"
source: "https://github.com/countercept/chainsaw"
license: "GPL3"
blackarch-pkgver: "v2.17.1.r0.gb746255"
---

# Installing chainsaw

> A powerful ‘first-response’ capability to quickly identify threats within Windows event logs.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/chainsaw) (version `v2.17.1.r0.gb746255`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S chainsaw    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/countercept/chainsaw/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/countercept/chainsaw) (the recipe uses a versioned archive).

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

Backlinks: [[defensive]] · [[chainsaw]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#chainsaw    # ad-hoc run, no install
nix profile install nixpkgs#chainsaw   # or persist it
```
