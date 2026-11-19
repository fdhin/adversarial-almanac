---
tool: "hostapd-wpe"
build-system: "make"
upstream: "https://w1.fi/hostapd/"
recipe: "blackarch"
aur-package: "hostapd-wpe"
source: "https://w1.fi/hostapd/"
license: "BSD-3-Clause"
blackarch-pkgver: "2.11"
kali-package: "hostapd-wpe"
kali-version: "2.10+git20220310-0kali3"
---

# Installing hostapd-wpe

> Modified hostapd to facilitate AP impersonation attacks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hostapd-wpe) (version `2.11`, license BSD-3-Clause).

## Arch / BlackArch

```bash
sudo pacman -S hostapd-wpe    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install hostapd-wpe    # Kali package version 2.10+git20220310-0kali3
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://w1.fi/hostapd/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `libnl`
  - `openssl-1.0`
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel

## Exact build steps from the recipe

```bash
make -C $_pkgname/${_pkgname}d
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[hostapd-wpe]] · [[BlackArch Tools]]

