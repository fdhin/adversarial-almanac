---
tool: "sdrpp"
build-system: "cmake"
upstream: "https://www.sdrpp.org"
recipe: "blackarch"
nix-package: "sdrpp"
source: "https://www.sdrpp.org"
license: "GPL3"
blackarch-pkgver: "nightly.r363.gf10fb98"
---

# Installing sdrpp

> The bloat-free SDR receiver.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sdrpp) (version `nightly.r363.gf10fb98`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S sdrpp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://github.com/AlexandreRouma/SDRPlusPlus.git
cd SDRPlusPlus
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `${_plugindeps[@]}`
  - `fftw`
  - `glew`
  - `glfw`
  - `libvolk`

## Exact build steps from the recipe

```bash
cmake -B build -S $pkgname \
    -DCMAKE_POLICY_VERSION_MINIMUM=3.5 \
		-DOPT_BUILD_BLADERF_SOURCE=ON \
		-DOPT_BUILD_LIMESDR_SOURCE=ON \
		-DOPT_BUILD_SDRPLAY_SOURCE=ON \
		-DOPT_BUILD_NEW_PORTAUDIO_SINK=ON \
		-DOPT_BUILD_M17_DECODER=ON
	make -C build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[radio]] · [[sdrpp]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#sdrpp    # ad-hoc run, no install
nix profile install nixpkgs#sdrpp   # or persist it
```
