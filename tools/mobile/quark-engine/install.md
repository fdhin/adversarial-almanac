---
tool: "quark-engine"
build-system: "python"
upstream: "https://github.com/quark-engine/quark-engine"
recipe: "blackarch"
nix-package: "quark-engine"
aur-package: "quark-engine"
source: "https://github.com/quark-engine/quark-engine"
license: "GPL"
blackarch-pkgver: "21.6.2"
---

# Installing quark-engine

> An Obfuscation-Neglect Android Malware Scoring System.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/quark-engine) (version `21.6.2`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S quark-engine    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/quark-engine/quark-engine) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `click`, `colorama`, `gitpython`, `graphviz`, `prettytable`, `setuptools`, `tqdm`
- **System libraries** (Arch name — translate for your distro):
  - `androguard`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[quark-engine]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#quark-engine    # ad-hoc run, no install
nix profile install nixpkgs#quark-engine   # or persist it
```
