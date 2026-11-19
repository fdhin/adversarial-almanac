---
tool: "androbugs"
build-system: "scripts"
upstream: "https://github.com/AndroBugs/AndroBugs_Framework"
recipe: "blackarch"
source: "https://github.com/AndroBugs/AndroBugs_Framework"
license: "GPL-3.0-or-later"
blackarch-pkgver: "7fd3a2c.r0.g7fd3a2c"
---

# Installing androbugs

> An efficient Android vulnerability scanner that helps developers or hackers find potential security vulnerabilities in Android applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/androbugs) (version `7fd3a2c.r0.g7fd3a2c`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S androbugs    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/AndroBugs/AndroBugs_Framework.git
cd AndroBugs_Framework
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
  - `radare2`
- ⚠️ Python 2 only deps: python2-elfesteem, python2-magic, python2-networkx, python2-pymongo, python2-pyside (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[androbugs]] · [[BlackArch Tools]]

