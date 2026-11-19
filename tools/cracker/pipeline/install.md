---
tool: "pipeline"
build-system: "other"
upstream: "https://github.com/hirnschallsebastian/Pipeline2"
recipe: "blackarch"
nix-package: "pipeline"
source: "https://github.com/hirnschallsebastian/Pipeline2"
license: "custom:unknown"
blackarch-pkgver: "19.f4935c9"
---

# Installing pipeline

> Designed to aid in targeted brute force password cracking attacks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pipeline) (version `19.f4935c9`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S pipeline    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/hirnschallsebastian/Pipeline2.git
cd Pipeline2
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  g++ main.cpp -o pipeline -std=c++11 -pthread
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[pipeline]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#pipeline    # ad-hoc run, no install
nix profile install nixpkgs#pipeline   # or persist it
```
