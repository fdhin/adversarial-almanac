---
tool: "reaver"
build-system: "autotools"
upstream: "https://github.com/t6x/reaver-wps-fork-t6x"
recipe: "arch-official/extra"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/reaver/-/raw/main/PKGBUILD"
version: "1.6.6"
license: "GPL2"
---

# Installing reaver

> brute force attack tool against Wi-Fi Protected Setup PIN number

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/reaver/-/raw/main/PKGBUILD) (version `1.6.6`, license GPL2).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S reaver
```

## Kali / Debian-based — same package name

```bash
sudo apt install reaver
```

## Any other Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/t6x/reaver-wps-fork-t6x.git
cd reaver-wps-fork-t6x
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd reaver-wps-fork-t6x-${pkgver}/src
  ./configure --prefix=/usr --sysconfdir=/etc
  make CONFDIR=/etc/reaver
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[wireless]] · [[reaver]] · [[Kali Gap Analysis]]
