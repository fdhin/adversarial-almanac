---
tool: "autopsy"
build-system: "scripts"
upstream: "https://github.com/sleuthkit/autopsy"
recipe: "blackarch"
nix-package: "autopsy"
aur-package: "autopsy"
source: "https://github.com/sleuthkit/autopsy"
license: "MIT, Apache-2.0"
blackarch-pkgver: "4.23.1"
kali-package: "autopsy"
kali-version: "2.24-6kali1"
---

# Installing autopsy

> The forensic browser. A GUI for the Sleuth Kit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/autopsy) (version `4.23.1`, license MIT, Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S autopsy    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install autopsy    # Kali package version 2.24-6kali1
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/sleuthkit/autopsy) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pytsk3`
- **System libraries** (Arch name — translate for your distro):
  - `java-runtime`
  - `sleuthkit`
  - `sleuthkit-java`
  - `testdisk`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/applications`, `/usr/share/pixmaps`

Backlinks: [[forensic]] · [[autopsy]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#autopsy    # ad-hoc run, no install
nix profile install nixpkgs#autopsy   # or persist it
```
