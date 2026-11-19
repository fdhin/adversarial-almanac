---
tool: "macchanger"
build-system: "autotools"
upstream: "https://www.gnu.org/software/macchanger"
recipe: "arch-official/extra"
nix-package: "macchanger"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/macchanger/-/raw/main/PKGBUILD"
version: "1.7.0"
license: "GPL"
---

# Installing macchanger

> utility for manipulating the MAC address of network interfaces

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/macchanger/-/raw/main/PKGBUILD) (version `1.7.0`, license GPL).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S macchanger
```

## Kali / Debian-based — same package name

```bash
sudo apt install macchanger
```

## Nix (works on any distro)

```bash
nix run nixpkgs#macchanger    # ad-hoc run, no install
nix profile install nixpkgs#macchanger   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/alobbs/macchanger) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd "${srcdir}/${pkgname}-${pkgver}"
  ./autogen.sh
  ./configure --prefix=/usr \
              --mandir=/usr/share/man \
              --infodir=/usr/share/info
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[wireless]] · [[macchanger]] · [[Kali Gap Analysis]]
