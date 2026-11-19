---
tool: "ipba2"
build-system: "scripts"
upstream: "http://www.ipbackupanalyzer.com/"
recipe: "blackarch"
source: "http://www.ipbackupanalyzer.com/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "95.c03bd85"
---

# Installing ipba2

> IOS Backup Analyzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ipba2) (version `95.c03bd85`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S ipba2    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/PicciMario/iPhone-Backup-Analyzer-2.git
cd iPhone-Backup-Analyzer-2
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
  - `qt4`
- ⚠️ Python 2 only deps: python2-pillow, python2-pyside, python2-six (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[ipba2]] · [[BlackArch Tools]]

