---
tool: "sqlitebrowser"
build-system: "cmake"
upstream: "https://sqlitebrowser.org"
recipe: "arch-official/extra"
nix-package: "sqlitebrowser"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/sqlitebrowser/-/raw/main/PKGBUILD"
version: "3.13.1"
license: "GPL-3.0-only"
---

# Installing sqlitebrowser

> GUI editor for SQLite databases

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/sqlitebrowser/-/raw/main/PKGBUILD) (version `3.13.1`, license GPL-3.0-only).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S sqlitebrowser
```

## Kali / Debian-based — same package name

```bash
sudo apt install sqlitebrowser
```

## Nix (works on any distro)

```bash
nix run nixpkgs#sqlitebrowser    # ad-hoc run, no install
nix profile install nixpkgs#sqlitebrowser   # or persist it
```

## Any other Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://github.com/sqlitebrowser/sqlitebrowser?signed
cd sqlitebrowser?signed
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `hicolor-icon-theme` — hicolor-icon-theme (any distro)
  - `libgcc`
  - `libqcustomplot.so`
  - `libqhexedit.so`
  - `libstdc++`
  - `ninja`
  - `qcustomplot`
  - `qhexedit2`
  - `qscintilla-qt5`
  - `qt5-base`
  - `qt5-svg`
  - `qt5-tools`
  - `sqlcipher`

## Exact build steps from the recipe

```bash
cd "${pkgname}"
	cmake -B build \
		-DCMAKE_INSTALL_PREFIX='/usr' \
		-DCMAKE_BUILD_TYPE='None' \
		-DBUILD_STABLE_VERSION='ON' \
		-DENABLE_TESTING='ON' \
		-DFORCE_INTERNAL_QCUSTOMPLOT='OFF' \
		-DFORCE_INTERNAL_QSCINTILLA='OFF' \
		-DFORCE_INTERNAL_QHEXEDIT='OFF' \
		-Dsqlcipher='1' \
		-Wno-dev \
		-G Ninja
	ninja -C build
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[database]] · [[sqlitebrowser]] · [[Kali Gap Analysis]]
