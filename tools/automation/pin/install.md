---
tool: "pin"
build-system: "make"
upstream: "https://software.intel.com/en-us/articles/pin-a-binary-instrumentation-tool-downloads"
recipe: "blackarch"
aur-package: "pin"
source: "https://software.intel.com/en-us/articles/pin-a-binary-instrumentation-tool-downloads"
license: "custom"
blackarch-pkgver: "3.28.r98749"
---

# Installing pin

> A dynamic binary instrumentation tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pin) (version `3.28.r98749`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S pin    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://software.intel.com/en-us/articles/pin-a-binary-instrumentation-tool-downloads) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `gcc`, `make`
- **System libraries** (Arch name — translate for your distro):
  - `lib32-gcc-libs`

## Exact build steps from the recipe

```bash
cd "$pkgname-$_pkgver-gcc-linux/source/tools/ManualExamples"

  TARGET=intel64 make

  TARGET=ia32 make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[pin]] · [[BlackArch Tools]]

