---
tool: "hostapd-mana"
build-system: "make"
upstream: "https://github.com/sensepost/hostapd-mana"
recipe: "blackarch"
nix-package: "hostapd-mana"
aur-package: "hostapd-mana"
source: "https://github.com/sensepost/hostapd-mana"
license: "custom:BSD/GPL v2 dual"
blackarch-pkgver: "2.6.5.r20.g8853d5a"
---

# Installing hostapd-mana

> Modified hostapd for Wi-Fi attacks to create a rogue access point.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hostapd-mana) (version `2.6.5.r20.g8853d5a`, license custom:BSD/GPL v2 dual).

## Arch / BlackArch

```bash
sudo pacman -S hostapd-mana    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/sensepost/hostapd-mana) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libnl`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd "$pkgname"

  make -C hostapd
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[wireless]] · [[hostapd-mana]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#hostapd-mana    # ad-hoc run, no install
nix profile install nixpkgs#hostapd-mana   # or persist it
```
