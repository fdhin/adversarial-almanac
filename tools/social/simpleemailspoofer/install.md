---
tool: "simpleemailspoofer"
build-system: "scripts"
upstream: "https://github.com/lunarca/SimpleEmailSpoofer"
recipe: "blackarch"
source: "https://github.com/lunarca/SimpleEmailSpoofer"
license: "MIT"
blackarch-pkgver: "54.7075f0c"
---

# Installing simpleemailspoofer

> A simple Python CLI to spoof emails.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/simpleemailspoofer) (version `54.7075f0c`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S simpleemailspoofer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/lunarca/SimpleEmailSpoofer.git
cd SimpleEmailSpoofer
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-colorama, python2-pysqlite (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[simpleemailspoofer]] · [[BlackArch Tools]]

