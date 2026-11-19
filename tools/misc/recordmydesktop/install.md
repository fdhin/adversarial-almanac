---
tool: "recordmydesktop"
build-system: "autotools"
upstream: "https://github.com/Enselic/recordmydesktop/"
recipe: "arch-official/extra"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/recordmydesktop/-/raw/main/PKGBUILD"
version: "0.4.0"
license: "GPL"
---

# Installing recordmydesktop

> Captures audio-video data of a Linux desktop session

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/recordmydesktop/-/raw/main/PKGBUILD) (version `0.4.0`, license GPL).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S recordmydesktop
```

## Kali / Debian-based — same package name

```bash
sudo apt install recordmydesktop
```

## Any other Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/Enselic/recordmydesktop/.git
cd recordmydesktop
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `alsa-lib` — Debian libasound2-dev / Fedora alsa-lib-devel
  - `jack`
  - `libsm`
  - `libtheora`
  - `libvorbis`
  - `libxdamage`
  - `libxext`
  - `popt`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd "$srcdir"/${pkgname}-${pkgver}
  ./configure --prefix=/usr --enable-jack=yes
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[misc]] · [[recordmydesktop]] · [[Kali Gap Analysis]]
