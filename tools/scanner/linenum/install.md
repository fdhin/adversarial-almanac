---
tool: "linenum"
build-system: "scripts"
upstream: "https://github.com/rebootuser/LinEnum"
recipe: "blackarch"
source: "https://github.com/rebootuser/LinEnum"
license: "GPL3"
blackarch-pkgver: "75.c47f9b2"
---

# Installing linenum

> Scripted Local Linux Enumeration & Privilege Escalation Checks

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/linenum) (version `75.c47f9b2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S linenum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/rebootuser/LinEnum.git
cd LinEnum
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[linenum]] · [[BlackArch Tools]]

