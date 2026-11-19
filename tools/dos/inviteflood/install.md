---
tool: "inviteflood"
build-system: "make"
upstream: "https://github.com/foreni-packages/inviteflood"
recipe: "blackarch"
source: "https://github.com/foreni-packages/inviteflood"
license: "GPL-2.0-or-later"
blackarch-pkgver: "2.0"
kali-package: "inviteflood"
kali-version: "2.0-1kali4"
---

# Installing inviteflood

> Flood a device with INVITE requests.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/inviteflood) (version `2.0`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S inviteflood    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install inviteflood    # Kali package version 2.0-1kali4
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/foreni-packages/inviteflood) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-upstream-$pkgver"
  
  make -C hack_library
  make -C "$pkgname"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[dos]] · [[inviteflood]] · [[BlackArch Tools]]

