---
tool: "triton"
build-system: "cmake"
upstream: "https://github.com/JonathanSalwan/Triton"
recipe: "blackarch"
nix-package: "triton"
aur-package: "triton"
source: "https://github.com/JonathanSalwan/Triton"
license: "BSD"
blackarch-pkgver: "4440.0ff03150"
---

# Installing triton

> A Dynamic Binary Analysis (DBA) framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/triton) (version `4440.0ff03150`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S triton    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/JonathanSalwan/Triton) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`
- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `boost`
  - `capstone`
  - `python`
  - `z3`

## Exact build steps from the recipe

```bash
cd $pkgname

  mkdir -p build

  cd build

  cmake -DCMAKE_INSTALL_PREFIX=/usr ..

  make

  #python2 setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[triton]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#triton    # ad-hoc run, no install
nix profile install nixpkgs#triton   # or persist it
```
