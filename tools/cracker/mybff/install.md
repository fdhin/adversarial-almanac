---
tool: "mybff"
build-system: "scripts"
upstream: "https://github.com/MooseDojo/myBFF"
recipe: "blackarch"
source: "https://github.com/MooseDojo/myBFF"
license: "BSD"
blackarch-pkgver: "99.ba4de8d"
---

# Installing mybff

> A Brute Force Framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mybff) (version `99.ba4de8d`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S mybff    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/MooseDojo/myBFF.git
cd myBFF
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-lxml, python2-requests, python2-selenium (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[mybff]] · [[BlackArch Tools]]

