---
tool: "bmc-tools"
build-system: "scripts"
upstream: "https://github.com/ANSSI-FR/bmc-tools"
recipe: "blackarch"
nix-package: "bmc-tools"
source: "https://github.com/ANSSI-FR/bmc-tools"
license: "custom:unknown"
blackarch-pkgver: "38.5a4cad3"
---

# Installing bmc-tools

> RDP Bitmap Cache parser.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bmc-tools) (version `38.5a4cad3`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S bmc-tools    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ANSSI-FR/bmc-tools) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[bmc-tools]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#bmc-tools    # ad-hoc run, no install
nix profile install nixpkgs#bmc-tools   # or persist it
```
