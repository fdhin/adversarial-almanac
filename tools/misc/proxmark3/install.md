---
tool: "proxmark3"
build-system: "make"
upstream: "https://github.com/RfidResearchGroup/proxmark3"
recipe: "arch-official/extra"
nix-package: "proxmark3"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/proxmark3/-/raw/main/PKGBUILD"
version: "4.20142"
license: "GPL2"
---

# Installing proxmark3

> Firmware, flasher, and client for the Proxmark3

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/proxmark3/-/raw/main/PKGBUILD) (version `4.20142`, license GPL2).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S proxmark3
```

## Kali / Debian-based — same package name

```bash
sudo apt install proxmark3
```

## Nix (works on any distro)

```bash
nix run nixpkgs#proxmark3    # ad-hoc run, no install
nix profile install nixpkgs#proxmark3   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/RfidResearchGroup/proxmark3.git
cd proxmark3
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `arm-none-eabi-gcc`
  - `arm-none-eabi-newlib`
  - `bluez-libs` — Debian libbluetooth-dev / Fedora bluez-libs-devel
  - `jansson`
  - `lua52`
  - `python`
  - `qt5-base`

## Exact build steps from the recipe

```bash
cd "$srcdir"/$pkgname-$pkgver

  make PLATFORM=PM3GENERIC SKIPQT=0 DONT_BUILD_NATIVE=y all

  cd "$srcdir"/$pkgname-$pkgver-rdv4

  make PLATFORM=PM3RDV4 FWTAG=rdv4 DONT_BUILD_NATIVE=y common bootrom recovery fullimage
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[misc]] · [[proxmark3]] · [[Kali Gap Analysis]]
