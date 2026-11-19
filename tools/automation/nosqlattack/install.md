---
tool: "nosqlattack"
build-system: "scripts"
upstream: "https://github.com/youngyangyang04/NoSQLAttack"
recipe: "blackarch"
source: "https://github.com/youngyangyang04/NoSQLAttack"
license: "GPL3"
blackarch-pkgver: "98.a5b0329"
---

# Installing nosqlattack

> Python tool to automate exploit MongoDB server IP on Internet anddisclose the database data by MongoDB default configuration weaknesses and injection attacks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nosqlattack) (version `98.a5b0329`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S nosqlattack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/youngyangyang04/NoSQLAttack.git
cd NoSQLAttack
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `mongodb`
  - `python2`
- ⚠️ Python 2 only deps: python2-httplib2, python2-ipcalc, python2-pymongo, python2-requests, python2-setuptools, python2-shodan (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[nosqlattack]] · [[BlackArch Tools]]

