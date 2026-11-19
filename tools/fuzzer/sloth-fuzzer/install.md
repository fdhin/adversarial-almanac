---
tool: "sloth-fuzzer"
build-system: "autotools"
upstream: "https://github.com/mfontanini/sloth-fuzzer"
recipe: "blackarch"
source: "https://github.com/mfontanini/sloth-fuzzer"
license: "custom:unknown"
blackarch-pkgver: "40.0f0cf2e"
---

# Installing sloth-fuzzer

> A smart file fuzzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sloth-fuzzer) (version `40.0f0cf2e`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S sloth-fuzzer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/mfontanini/sloth-fuzzer) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bison`
  - `crypto++`
  - `flex`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[sloth-fuzzer]] · [[BlackArch Tools]]

