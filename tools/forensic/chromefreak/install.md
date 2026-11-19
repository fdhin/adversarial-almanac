---
tool: "chromefreak"
build-system: "scripts"
upstream: "http://osandamalith.github.io/ChromeFreak/"
recipe: "blackarch"
source: "http://osandamalith.github.io/ChromeFreak/"
license: "custom"
blackarch-pkgver: "24.12745b1"
---

# Installing chromefreak

> A Cross-Platform Forensic Framework for Google Chrome

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/chromefreak) (version `24.12745b1`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S chromefreak    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/OsandaMalith/ChromeFreak.git
cd ChromeFreak
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pysqlite, python2-simplejson (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[forensic]] · [[chromefreak]] · [[BlackArch Tools]]

