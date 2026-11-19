---
tool: "cutycapt"
build-system: "cmake"
upstream: "https://cutycapt.sourceforge.net/"
recipe: "blackarch"
source: "https://cutycapt.sourceforge.net/"
license: "GPL-3.0-or-later"
blackarch-pkgver: "11.0"
kali-package: "cutycapt"
kali-version: "0.0+20200623-1"
---

# Installing cutycapt

> A Qt and WebEngine-based command-line utility that captures a web page's rendered output.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cutycapt) (version `11.0`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S cutycapt    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install cutycapt    # Kali package version 0.0+20200623-1
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://cutycapt.sourceforge.net/) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`
- **System libraries** (Arch name — translate for your distro):
  - `ninja`
  - `qt6-base`
  - `qt6-svg`
  - `qt6-webchannel`
  - `qt6-webengine`

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  cmake -S . -B build -GNinja
  ninja -C build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[cutycapt]] · [[BlackArch Tools]]

