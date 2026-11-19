---
tool: "nsearch"
build-system: "scripts"
upstream: "https://github.com/JKO/nsearch"
recipe: "blackarch"
source: "https://github.com/JKO/nsearch"
license: "APACHE"
blackarch-pkgver: "353.bd8205b"
---

# Installing nsearch

> Minimal script to help find script into the nse database.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nsearch) (version `353.bd8205b`, license APACHE).

## Arch / BlackArch

```bash
sudo pacman -S nsearch    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/JKO/nsearch) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pysqlite, python2-python-i18n, python2-python-nmap, python2-yaml (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[nsearch]] · [[BlackArch Tools]]

