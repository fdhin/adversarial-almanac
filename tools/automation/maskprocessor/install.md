---
tool: "maskprocessor"
build-system: "scripts"
upstream: "https://hashcat.net/wiki/doku.php?id=maskprocessor"
recipe: "blackarch"
nix-package: "maskprocessor"
aur-package: "maskprocessor"
source: "https://hashcat.net/wiki/doku.php?id=maskprocessor"
license: "custom"
blackarch-pkgver: "0.73"
kali-package: "maskprocessor"
kali-version: "0.73+git20170609.1708898-4"
---

# Installing maskprocessor

> A High-Performance word generator with a per-position configurable charset.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/maskprocessor) (version `0.73`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S maskprocessor    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install maskprocessor    # Kali package version 0.73+git20170609.1708898-4
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://hashcat.net/wiki/doku.php?id=maskprocessor) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `p7zip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[maskprocessor]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#maskprocessor    # ad-hoc run, no install
nix profile install nixpkgs#maskprocessor   # or persist it
```
