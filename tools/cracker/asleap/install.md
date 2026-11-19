---
tool: "asleap"
build-system: "make"
upstream: "https://github.com/OscarAkaElvis/asleap"
recipe: "blackarch"
nix-package: "asleap"
aur-package: "asleap"
source: "https://github.com/OscarAkaElvis/asleap"
license: "GPL-2.0-or-later"
blackarch-pkgver: "2.5"
kali-package: "asleap"
kali-version: "2.5-1"
---

# Installing asleap

> Cisco LEAP and generic MS-CHAPv2 dictionary Attack.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/asleap) (version `2.5`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S asleap    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install asleap    # Kali package version 2.5-1
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/OscarAkaElvis/asleap) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[asleap]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#asleap    # ad-hoc run, no install
nix profile install nixpkgs#asleap   # or persist it
```
