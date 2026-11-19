---
tool: "7zip"
build-system: "make"
upstream: "https://www.7-zip.org"
recipe: "arch-official/extra"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/7zip/-/raw/main/PKGBUILD"
version: "26.05"
license: "LGPL-2.1-or-later AND LicenseRef-UnRAR AND BSD-3-Clause AND BSD-2-Clause"
---

# Installing 7zip

> 7-Zip file archiver with a high compression ratio

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/7zip/-/raw/main/PKGBUILD) (version `26.05`, license LGPL-2.1-or-later AND LicenseRef-UnRAR AND BSD-3-Clause AND BSD-2-Clause).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S 7zip
```

## Kali / Debian-based — same package name

```bash
sudo apt install 7zip
```

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://www.7-zip.org/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
local _platform_flags=()

  case $CARCH in
    x86_64)
      _platform_flags=(PLATFORM=x64 IS_X64=1 MY_ASM=uasm USE_ASM=1)
      ;;
    aarch64)
      _platform_flags=(PLATFORM=arm64 IS_ARM64=1 USE_ASM=1)
      ;;
  esac

  for component in Bundles/{Alone,Alone7z,Format7zF,SFXCon} UI/Console; do
    make -C CPP/7zip/$component -f ../../cmpl_gcc.mak "${_platform_flags[@]}" \
      LFLAGS_STRIP= \
      CC="cc $CPPFLAGS $CFLAGS $LDFLAGS" \
      CXX="g++ $CPPFLAGS $CXXFLAGS $LDFLAGS"
  done
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[forensic]] · [[7zip]] · [[Kali Gap Analysis]]
