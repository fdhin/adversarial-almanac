---
tool: "xsser"
build-system: "python"
upstream: "https://xsser.03c8.net/"
recipe: "blackarch"
nix-package: "xsser"
source: "https://xsser.03c8.net/"
license: "GPL3"
blackarch-pkgver: "1.9"
kali-package: "xsser"
kali-version: "1.8.4-0kali3"
---

# Installing xsser

> A penetration testing tool for detecting and exploiting XSS vulnerabilites.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xsser) (version `1.9`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S xsser    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install xsser    # Kali package version 1.8.4-0kali3
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://xsser.03c8.net/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `pycurl`, `setuptools`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[xsser]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#xsser    # ad-hoc run, no install
nix profile install nixpkgs#xsser   # or persist it
```
