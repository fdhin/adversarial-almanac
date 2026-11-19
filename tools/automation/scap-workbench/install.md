---
tool: "scap-workbench"
build-system: "cmake"
upstream: "https://github.com/OpenSCAP/scap-workbench"
recipe: "blackarch"
aur-package: "scap-workbench"
source: "https://github.com/OpenSCAP/scap-workbench"
license: "GPL2"
blackarch-pkgver: "1.2.1"
---

# Installing scap-workbench

> SCAP Scanner And Tailoring Graphical User Interface.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/scap-workbench) (version `1.2.1`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S scap-workbench    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://www.open-scap.org/tools/scap-workbench/) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `asciidoc`
  - `asciidoctor`
  - `libutil-linux`
  - `openscap`
  - `openssh-askpass`
  - `qt5-base`
  - `qt5-xmlpatterns`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  mkdir -p build
  cd build
  cmake -DCMAKE_INSTALL_PREFIX=/usr ..
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[scap-workbench]] · [[BlackArch Tools]]

