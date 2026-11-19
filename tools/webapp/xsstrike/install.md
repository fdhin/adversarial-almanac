---
tool: "xsstrike"
build-system: "scripts"
upstream: "https://github.com/UltimateHackers/XSStrike"
recipe: "blackarch"
nix-package: "xsstrike"
aur-package: "xsstrike"
source: "https://github.com/UltimateHackers/XSStrike"
license: "MIT"
blackarch-pkgver: "475.ab27955"
---

# Installing xsstrike

> An advanced XSS detection and exploitation suite.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xsstrike) (version `475.ab27955`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S xsstrike    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/UltimateHackers/XSStrike.git
cd XSStrike
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `fuzzywuzzy`, `levenshtein`, `prettytable`, `requests`, `selenium`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[xsstrike]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#xsstrike    # ad-hoc run, no install
nix profile install nixpkgs#xsstrike   # or persist it
```
