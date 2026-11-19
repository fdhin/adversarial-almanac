---
tool: "rz-ghidra"
build-system: "cmake"
upstream: "https://github.com/rizinorg/rz-ghidra"
recipe: "arch-official/extra"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/rz-ghidra/-/raw/main/PKGBUILD"
version: "0.8.0"
license: "LGPL-3.0-only"
---

# Installing rz-ghidra

> ghidra decompiler and sleigh disassembler for rizin

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/rz-ghidra/-/raw/main/PKGBUILD) (version `0.8.0`, license LGPL-3.0-only).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S rz-ghidra
```

## Kali / Debian-based — same package name

```bash
sudo apt install rz-ghidra
```

## Any other Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://github.com/rizinorg/rz-ghidra.git
cd rz-ghidra
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `rizin`
  - `rz-cutter`

## Exact build steps from the recipe

```bash
cd ${pkgname}
  cmake \
    -B build \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DCMAKE_BUILD_TYPE=None \
    -DBUILD_CUTTER_PLUGIN=ON \
    -DBUILD_SLASPECS=ON \
    -DCUTTER_INSTALL_PLUGDIR=/usr/share/rizin/cutter/plugins/native
  make -C build VERBOSE=1
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[forensic]] · [[rz-ghidra]] · [[Kali Gap Analysis]]
