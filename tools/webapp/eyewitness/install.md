---
tool: "eyewitness"
build-system: "scripts"
upstream: "https://github.com/ChrisTruncer/EyeWitness"
recipe: "blackarch"
nix-package: "eyewitness"
source: "https://github.com/ChrisTruncer/EyeWitness"
license: "GPL3"
blackarch-pkgver: "v20230525.1.r207.gc3286fb"
kali-package: "eyewitness"
kali-version: "20230525.1+git20230720-0kali4"
---

# Installing eyewitness

> Designed to take screenshots of websites, provide some server header info, and identify default credentials if possible.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/eyewitness) (version `v20230525.1.r207.gc3286fb`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S eyewitness    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install eyewitness    # Kali package version 20230525.1+git20230720-0kali4
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/FortyNorthSecurity/EyeWitness.git
cd EyeWitness
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `easyprocess`, `fuzzywuzzy`, `netaddr`, `pillow`, `pyqt4`, `pytesseract`, `pyvirtualdisplay`, `rdpy`, `rsa`, `selenium`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `firefox`
  - `geckodriver`
  - `ghost-py`
  - `python`
  - `xorg-server-xvfb`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[eyewitness]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#eyewitness    # ad-hoc run, no install
nix profile install nixpkgs#eyewitness   # or persist it
```
