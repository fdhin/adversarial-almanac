---
tool: "commix"
build-system: "scripts"
upstream: "https://github.com/commixproject/commix"
recipe: "blackarch"
nix-package: "commix"
aur-package: "commix"
source: "https://github.com/commixproject/commix"
license: "custom:unknown, AGPL"
blackarch-pkgver: "2639.12f975b7"
kali-package: "commix"
kali-version: "4.1-0kali1"
---

# Installing commix

> Automated All-in-One OS Command Injection and Exploitation Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/commix) (version `2639.12f975b7`, license custom:unknown, AGPL).

## Arch / BlackArch

```bash
sudo pacman -S commix    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install commix    # Kali package version 4.1-0kali1
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/commixproject/commix) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `scapy`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[commix]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#commix    # ad-hoc run, no install
nix profile install nixpkgs#commix   # or persist it
```
