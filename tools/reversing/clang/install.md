---
tool: "clang"
build-system: "cmake"
upstream: "https://clang.llvm.org/"
recipe: "arch-official/extra"
nix-package: "clang"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/clang/-/raw/main/PKGBUILD"
version: "23.1.1"
license: "Apache-2.0 WITH LLVM-exception"
provides: ["scan-build"]
---

# Installing clang

> C, C++ and Objective-C compiler (LLVM based), clang binary

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/clang/-/raw/main/PKGBUILD) (version `23.1.1`, license Apache-2.0 WITH LLVM-exception).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S clang
```

## Kali / Debian-based — same package name

```bash
sudo apt install clang
```

## Nix (works on any distro)

```bash
nix run nixpkgs#clang    # ad-hoc run, no install
nix profile install nixpkgs#clang   # or persist it
```

## Any other Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://clang.llvm.org/) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Python (pip)**: `myst-parser`, `sphinx`
- **Build tools**: `cmake`, `gcc`
- **System libraries** (Arch name — translate for your distro):
  - `compiler-rt`
  - `libgcc`
  - `libxml2` — Debian libxml2-dev / Fedora libxml2-devel
  - `llvm-libs`
  - `llvm=$pkgver`
  - `ninja`

## Exact build steps from the recipe

```bash
cd llvm-project-$pkgver.src/clang/build

  # Build only minimal debug info to reduce size
  CFLAGS=${CFLAGS/-g /-g1 }
  CXXFLAGS=${CXXFLAGS/-g /-g1 }

  local cmake_args=(
    -G Ninja
    -DCMAKE_BUILD_TYPE=Release
    -DCMAKE_INSTALL_PREFIX=/usr
    -DCMAKE_INSTALL_DOCDIR=share/doc
    -DCMAKE_SKIP_RPATH=ON
    -DCLANG_DEFAULT_PIE_ON_LINUX=ON
    -DCLANG_LINK_CLANG_DYLIB=ON
    -DENABLE_LINKER_BUILD_ID=ON
    -DLLVM_BUILD_DOCS=ON
    -DLLVM_BUILD_TESTS=ON
    -DLLVM_ENABLE_RTTI=ON
    -DLLVM_ENABLE_SPHINX=ON
    -DLLVM_EXTERNAL_CLANG_TOOLS_EXTRA_SOURCE_DIR="$srcdir/llvm-project-$pkgver.src/clang-tools-extra"
    -DLLVM_EXTERNAL_LIT=/usr/bin/lit
    -DLLVM_INCLUDE_DOCS=ON
    -DLLVM_LINK_LLVM_DYLIB=ON
    -DLLVM_THIRD_PARTY_DIR="$srcdir/llvm-project-$pkgver.src/third-party"
    -DSPHINX_WARNINGS_AS_ERRORS=OFF
    -DLLVM_PACKAGE_BUGREPORT=https://gitlab.archlinux.org/archlinux/packaging/packages/clang/-/issues
  )

  cmake .. "${cmake_args[@]}"
  local distribution_components=$(_get_distribution_components | paste -sd\;)
  test -n "$distribution_components"
  cmake_args+=(-DLLVM_DISTRIBUTION_COMPONENTS="$distribution_components")

  cmake .. "${cmake_args[@]}"
  ninja
```

## Installed layout (per recipe)

- Commands: `/usr/bin/scan-build`
- Data: `/usr/share/bash-completion`, `/usr/share/clang`

Backlinks: [[reversing]] · [[clang]] · [[Kali Gap Analysis]]
