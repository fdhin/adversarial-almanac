---
tool: "graffiti"
build-system: "scripts"
upstream: "https://github.com/Ekultek/Graffiti"
recipe: "blackarch"
source: "https://github.com/Ekultek/Graffiti"
license: "custom:unknown"
blackarch-pkgver: "24.4af61b4"
---

# Installing graffiti

> A tool to generate obfuscated one liners to aid in penetration testing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/graffiti) (version `24.4af61b4`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S graffiti    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Ekultek/Graffiti.git
cd Graffiti
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-crypto (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[graffiti]] · [[BlackArch Tools]]

