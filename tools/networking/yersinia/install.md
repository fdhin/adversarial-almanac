---
tool: "yersinia"
build-system: "autotools"
upstream: "https://github.com/tomac/yersinia"
recipe: "blackarch"
nix-package: "yersinia"
aur-package: "yersinia"
source: "https://github.com/tomac/yersinia"
license: "GPL-2.0-or-later"
blackarch-pkgver: "0.8.2"
kali-package: "yersinia"
kali-version: "0.8.2-2.4"
---

# Installing yersinia

> A network tool designed to take advantage of some weakness in different network protocols.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/yersinia) (version `0.8.2`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S yersinia    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install yersinia    # Kali package version 0.8.2-2.4
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/tomac/yersinia) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `gtk2`
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  if [[ "$CARCH" == "i686" || "$CARCH" == "x86_64" ]];
  then
    ./configure --prefix=/usr --sbindir=/usr/bin \
      --with-pcap-includes=/usr/include/
  else
    ./configure --build=arm --prefix=/usr --sbindir=/usr/bin \
      --with-pcap-includes=/usr/include/
  fi

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[yersinia]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#yersinia    # ad-hoc run, no install
nix profile install nixpkgs#yersinia   # or persist it
```
