---
tool: "cantoolz"
build-system: "scripts"
upstream: "https://github.com/CANToolz/CANToolz"
recipe: "blackarch"
nix-package: "cantoolz"
source: "https://github.com/CANToolz/CANToolz"
license: "Apache"
blackarch-pkgver: "425.82d330b"
---

# Installing cantoolz

> Framework for black-box CAN network analysis.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cantoolz) (version `425.82d330b`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S cantoolz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/CANToolz/CANToolz.git
cd CANToolz
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `bitstring`, `flask`, `mido`, `numpy`, `pyserial`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automobile]] · [[cantoolz]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#cantoolz    # ad-hoc run, no install
nix profile install nixpkgs#cantoolz   # or persist it
```
