---
tool: "weevely"
build-system: "scripts"
upstream: "http://epinna.github.io/Weevely/"
recipe: "blackarch"
nix-package: "weevely"
aur-package: "weevely"
source: "http://epinna.github.io/Weevely/"
license: "GPL3"
blackarch-pkgver: "928.3d62e88"
kali-package: "weevely"
kali-version: "4.0.2-1"
---

# Installing weevely

> Weaponized web shell.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/weevely) (version `928.3d62e88`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S weevely    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install weevely    # Kali package version 4.0.2-1
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/epinna/weevely3.git
cd weevely3
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `dateutil`, `mako`, `prettytable`, `pyopenssl`, `pysocks`, `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[webapp]] · [[weevely]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#weevely    # ad-hoc run, no install
nix profile install nixpkgs#weevely   # or persist it
```
