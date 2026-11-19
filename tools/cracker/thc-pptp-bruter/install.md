---
tool: "thc-pptp-bruter"
build-system: "autotools"
upstream: "http://www.thc.org"
recipe: "blackarch"
source: "https://github.com/BlackArch/thc-pptp-bruter.git"
license: "GPL"
blackarch-pkgver: "0.1.4"
kali-package: "thc-pptp-bruter"
kali-version: "0.1.4-1kali5"
---

# Installing thc-pptp-bruter

> A brute force program that works against pptp vpn endpoints (tcp port 1723).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/thc-pptp-bruter) (version `0.1.4`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S thc-pptp-bruter    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install thc-pptp-bruter    # Kali package version 0.1.4-1kali5
```

## Any Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/BlackArch/thc-pptp-bruter.git
cd thc-pptp-bruter
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `gcc`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  if [[ "$CARCH" == "i686" || "$CARCH" == "x86_64" ]];
  then
    ./configure --prefix=/usr
  else
    ./configure --build=arm --prefix=/usr
  fi

  make CFLAGS="$CFLAGS"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[thc-pptp-bruter]] · [[BlackArch Tools]]

