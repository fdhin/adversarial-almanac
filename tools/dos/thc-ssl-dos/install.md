---
tool: "thc-ssl-dos"
build-system: "autotools"
upstream: "http://www.thc.org/thc-ssl-dos/"
recipe: "blackarch"
aur-package: "thc-ssl-dos"
source: "https://github.com/BlackArch/thc-ssl-dos.git"
license: "custom:unknown"
blackarch-pkgver: "1.4"
kali-package: "thc-ssl-dos"
kali-version: "1.4-1kali6"
---

# Installing thc-ssl-dos

> A tool to verify the performance of SSL. To be used in your authorized and legitimate area ONLY. You need to accept this to make use of it, no use for bad intentions, you have been warned!

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/thc-ssl-dos) (version `1.4`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S thc-ssl-dos    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install thc-ssl-dos    # Kali package version 1.4-1kali6
```

## Any Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/BlackArch/thc-ssl-dos.git
cd thc-ssl-dos
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  if [[ "$CARCH" == "i686" || "$CARCH" == "x86_64" ]];
  then
    ./configure --prefix=/usr
  else
    ./configure --build=arm --prefix=/usr
  fi

  make all
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[dos]] · [[thc-ssl-dos]] · [[BlackArch Tools]]

