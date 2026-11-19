---
tool: "hotpatch"
build-system: "cmake"
upstream: "http://www.selectiveintellect.com/hotpatch.html"
recipe: "blackarch"
nix-package: "hotpatch"
source: "http://www.selectiveintellect.com/hotpatch.html"
license: "BSD"
blackarch-pkgver: "90.fd2baf1"
---

# Installing hotpatch

> Hot patches executables on Linux using .so file injection.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hotpatch) (version `90.fd2baf1`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S hotpatch    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](http://www.selectiveintellect.com/hotpatch.html) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[hotpatch]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#hotpatch    # ad-hoc run, no install
nix profile install nixpkgs#hotpatch   # or persist it
```
