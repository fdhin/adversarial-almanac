---
tool: "aircrack-ng"
build-system: "python"
upstream: "https://www.aircrack-ng.org"
recipe: "arch-official/extra"
nix-package: "aircrack-ng"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/aircrack-ng/-/raw/main/PKGBUILD"
version: "${_pkgver//-/}"
license: "GPL-2.0-only, BSD-3-Clause, OpenSSL"
---

# Installing aircrack-ng

> wireless WEP/WPA cracking utilities

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/aircrack-ng/-/raw/main/PKGBUILD) (version `${_pkgver//-/}`, license GPL-2.0-only, BSD-3-Clause, OpenSSL).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S aircrack-ng
```

## Kali / Debian-based — same package name

```bash
sudo apt install aircrack-ng
```

## Nix (works on any distro)

```bash
nix run nixpkgs#aircrack-ng    # ad-hoc run, no install
nix profile install nixpkgs#aircrack-ng   # or persist it
```

## Any other Linux distro

**B. From source:**

**Source:** download the release from the [upstream site](https://www.aircrack-ng.org/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`
- **System libraries** (Arch name — translate for your distro):
  - `ethtool`
  - `glibc`
  - `hwloc`
  - `iw`
  - `libgcc`
  - `libnl`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `libstdc++`
  - `net-tools`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `pcre`
  - `python`
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel
  - `usbutils`
  - `wireless_tools` — Debian wireless-tools / Fedora wireless-tools
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd $pkgname-$_pkgver

  ./configure \
    --prefix=/usr \
    --libexecdir=/usr/lib \
    --sbindir=/usr/bin \
    --with-ext-scripts \
    --with-experimental

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[wireless]] · [[aircrack-ng]] · [[Kali Gap Analysis]]
