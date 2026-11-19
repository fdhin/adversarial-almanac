---
tool: "skypefreak"
build-system: "scripts"
upstream: "http://osandamalith.github.io/SkypeFreak/"
recipe: "blackarch"
source: "http://osandamalith.github.io/SkypeFreak/"
license: "GPL3"
blackarch-pkgver: "33.9347a65"
---

# Installing skypefreak

> A Cross Platform Forensic Framework for Skype.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/skypefreak) (version `33.9347a65`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S skypefreak    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/OsandaMalith/SkypeFreak.git
cd SkypeFreak
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pysqlite (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[skypefreak]] · [[BlackArch Tools]]

