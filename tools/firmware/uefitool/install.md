---
tool: "uefitool"
build-system: "cmake"
upstream: "https://github.com/LongSoft/UEFITool"
recipe: "upstream"
license: "see repository"
---

# Installing uefitool

> Cross-platform UEFI firmware image viewer/editor (Qt GUI) with UEFIExtract/UEFIFind console companions built from the same engine.

Source: upstream [`LongSoft/UEFITool`](https://github.com/LongSoft/UEFITool) (active line: `new_engine` branch).

## Prebuilt / releases

[Upstream releases](https://github.com/LongSoft/UEFITool/releases) ship prebuilt UEFITool, UEFIExtract and UEFIFind binaries for Linux, macOS and Windows.

## Arch Linux

```bash
yay -S uefitool    # AUR
```

## Nix (works on any distro)

```bash
nix run nixpkgs#uefitool    # ad-hoc run, no install
nix profile install nixpkgs#uefitool
```

## Build from source

The Qt GUI needs a C++ compiler plus Qt5 or Qt6:

```bash
git clone https://github.com/LongSoft/UEFITool.git
cd UEFITool
qmake ./UEFITool/uefitool.pro
make release
```

Qt6 builds can use CMake instead of qmake. The non-Qt console tools build with CMake or Meson against the same tree:

```bash
cmake UEFIExtract && make release
cmake UEFIFind && make release
```

Backlinks: [[firmware]] · [[uefitool]]
