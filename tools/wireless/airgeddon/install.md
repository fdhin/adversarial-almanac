---
tool: "airgeddon"
build-system: "scripts"
upstream: "https://github.com/v1s1t0r1sh3r3/airgeddon"
recipe: "blackarch"
nix-package: "airgeddon"
aur-package: "airgeddon"
source: "https://github.com/v1s1t0r1sh3r3/airgeddon"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v12.02.r0.g700ad7d"
kali-package: "airgeddon"
kali-version: "12.02-0kali1"
---

# Installing airgeddon

> Multi-use bash script for Linux systems to audit wireless networks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/airgeddon) (version `v12.02.r0.g700ad7d`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S airgeddon    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install airgeddon    # Kali package version 12.02-0kali1
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/v1s1t0r1sh3r3/airgeddon) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `aircrack-ng`
  - `bash`
  - `coreutils`
  - `gawk`
  - `iproute2`
  - `iw`
  - `pciutils`
  - `procps-ng`
  - `sed`
  - `tmux`
  - `xterm`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[airgeddon]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#airgeddon    # ad-hoc run, no install
nix profile install nixpkgs#airgeddon   # or persist it
```
