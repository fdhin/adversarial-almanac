---
tool: "jsparser"
build-system: "scripts"
upstream: "https://github.com/nahamsec/JSParser"
recipe: "blackarch"
source: "https://github.com/nahamsec/JSParser"
license: "custom:unknown"
blackarch-pkgver: "31.ccd3ab6"
---

# Installing jsparser

> Parse javascript using Tornado and JSBeautifier to discover interesting enpoints.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jsparser) (version `31.ccd3ab6`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S jsparser    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/nahamsec/JSParser.git
cd JSParser
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-jsbeautifier, python2-netaddr, python2-pycurl, python2-safeurl, python2-tornado (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[jsparser]] · [[BlackArch Tools]]

