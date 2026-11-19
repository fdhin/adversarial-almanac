---
tool: "tekdefense-automater"
build-system: "scripts"
upstream: "https://github.com/1aN0rmus/TekDefense-Automater"
recipe: "blackarch"
source: "https://github.com/1aN0rmus/TekDefense-Automater"
license: "MIT"
blackarch-pkgver: "88.42548cf"
provides: ["automater"]
---

# Installing tekdefense-automater

> IP URL and MD5 OSINT Analysis

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tekdefense-automater) (version `88.42548cf`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S tekdefense-automater    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/1aN0rmus/TekDefense-Automater.git
cd TekDefense-Automater
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- Commands: `/usr/bin/automater`

Backlinks: [[forensic]] · [[tekdefense-automater]] · [[BlackArch Tools]]

