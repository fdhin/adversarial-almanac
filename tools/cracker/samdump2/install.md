---
tool: "samdump2"
build-system: "scripts"
upstream: "http://sourceforge.net/projects/ophcrack/files/samdump2/"
recipe: "blackarch"
nix-package: "samdump2"
aur-package: "samdump2"
source: "http://sourceforge.net/projects/ophcrack/files/samdump2/"
license: "GPL"
blackarch-pkgver: "3.0.0"
kali-package: "samdump2"
kali-version: "3.0.0-8"
---

# Installing samdump2

> Dump password hashes from a Windows NT/2k/XP installation

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/samdump2) (version `3.0.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S samdump2    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install samdump2    # Kali package version 3.0.0-8
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/ophcrack/files/samdump2/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[samdump2]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#samdump2    # ad-hoc run, no install
nix profile install nixpkgs#samdump2   # or persist it
```
