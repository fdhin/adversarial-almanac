---
tool: "rainbowcrack"
build-system: "scripts"
upstream: "http://project-rainbowcrack.com/"
recipe: "blackarch"
nix-package: "rainbowcrack"
aur-package: "rainbowcrack"
source: "http://project-rainbowcrack.com/"
license: "custom:unknown"
blackarch-pkgver: "1.8"
kali-package: "rainbowcrack"
kali-version: "1.8-0kali3"
---

# Installing rainbowcrack

> Password cracker based on the faster time-memory trade-off. With MySQL and Cisco PIX Algorithm patches.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rainbowcrack) (version `1.8`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S rainbowcrack    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install rainbowcrack    # Kali package version 1.8-0kali3
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://project-rainbowcrack.com/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `gcc-libs`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[rainbowcrack]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#rainbowcrack    # ad-hoc run, no install
nix profile install nixpkgs#rainbowcrack   # or persist it
```
