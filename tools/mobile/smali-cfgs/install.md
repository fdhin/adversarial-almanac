---
tool: "smali-cfgs"
build-system: "scripts"
upstream: "https://github.com/ch0psticks/Smali-CFGs"
recipe: "blackarch"
source: "https://github.com/ch0psticks/Smali-CFGs"
license: "GPL2"
blackarch-pkgver: "6.4450418"
---

# Installing smali-cfgs

> Smali Control Flow Graph's.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/smali-cfgs) (version `6.4450418`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S smali-cfgs    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/ch0psticks/Smali-CFGs.git
cd Smali-CFGs
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pydot (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[smali-cfgs]] · [[BlackArch Tools]]

