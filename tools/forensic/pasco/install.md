---
tool: "pasco"
build-system: "scripts"
upstream: "https://www.jonesdykstra.com/"
recipe: "blackarch"
nix-package: "pasco"
source: "https://www.jonesdykstra.com/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "20040505_1"
kali-package: "pasco"
kali-version: "20040505-4"
---

# Installing pasco

> Examines the contents of Internet Explorer's cache files for forensic purposes.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pasco) (version `20040505_1`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S pasco    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install pasco    # Kali package version 20040505-4
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.jonesdykstra.com/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[pasco]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#pasco    # ad-hoc run, no install
nix profile install nixpkgs#pasco   # or persist it
```
