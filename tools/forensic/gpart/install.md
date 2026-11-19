---
tool: "gpart"
build-system: "make"
upstream: "https://github.com/baruch/gpart"
recipe: "arch-official/extra"
nix-package: "gpart"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/gpart/-/raw/main/PKGBUILD"
version: "0.3"
license: "GPL"
---

# Installing gpart

> Guess PC disk partition table, find lost partitions

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/gpart/-/raw/main/PKGBUILD) (version `0.3`, license GPL).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S gpart
```

## Kali / Debian-based — same package name

```bash
sudo apt install gpart
```

## Nix (works on any distro)

```bash
nix run nixpkgs#gpart    # ad-hoc run, no install
nix profile install nixpkgs#gpart   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/baruch/gpart.git
cd gpart
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd "${srcdir}/${pkgname}-${pkgver}"

  ./autogen.sh
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[forensic]] · [[gpart]] · [[Kali Gap Analysis]]
