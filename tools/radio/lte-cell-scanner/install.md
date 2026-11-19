---
tool: "lte-cell-scanner"
build-system: "cmake"
upstream: "https://github.com/JiaoXianjun/LTE-Cell-Scanner"
recipe: "blackarch"
source: "https://github.com/JiaoXianjun/LTE-Cell-Scanner"
license: "GPL"
blackarch-pkgver: "26.e7f71cb"
---

# Installing lte-cell-scanner

> An OpenCL accelerated TDD/FDD LTE Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lte-cell-scanner) (version `26.e7f71cb`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S lte-cell-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://github.com/JiaoXianjun/LTE-Cell-Scanner
cd LTE-Cell-Scanner
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `bladerf`
  - `boost`
  - `boost-libs`
  - `fftw`
  - `hackrf`
  - `itpp`

## Exact build steps from the recipe

```bash
cd $pkgname

	cd build

	cmake ../ \
    -DCMAKE_BUILD_TYPE=Release \
		-DCMAKE_INSTALL_PREFIX=/usr \
		-DCMAKE_VERBOSE_MAKEFILE=ON \
		-DUSE_HACKRF=1 \
		-DUSE_BLADERF=1 \
		-DUSE_OPENCL=0 # opencl segfaults in some cases

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[radio]] · [[lte-cell-scanner]] · [[BlackArch Tools]]

