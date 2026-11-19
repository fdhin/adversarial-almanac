---
tool: "fwbuilder"
build-system: "make"
upstream: "https://github.com/fwbuilder"
recipe: "arch-official/extra"
nix-package: "fwbuilder"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/fwbuilder/-/raw/main/PKGBUILD"
version: "5.3.7"
license: "GPL-2.0-or-later"
---

# Installing fwbuilder

> Firewall administration tool GUI

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/fwbuilder/-/raw/main/PKGBUILD) (version `5.3.7`, license GPL-2.0-or-later).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S fwbuilder
```

## Kali / Debian-based — same package name

```bash
sudo apt install fwbuilder
```

## Nix (works on any distro)

```bash
nix run nixpkgs#fwbuilder    # ad-hoc run, no install
nix profile install nixpkgs#fwbuilder   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/fwbuilder.git
cd fwbuilder
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `desktop-file-utils`
  - `hicolor-icon-theme` — hicolor-icon-theme (any distro)
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `net-snmp`
  - `qt5-base`
  - `shared-mime-info`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"
    ./autogen.sh --prefix=/usr
    make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout
- Data: `/usr/share/applications`, `/usr/share/mime`

Backlinks: [[defensive]] · [[fwbuilder]] · [[Kali Gap Analysis]]
