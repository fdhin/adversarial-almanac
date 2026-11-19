---
tool: "wascan"
build-system: "scripts"
upstream: "https://github.com/m4ll0k/WAScan"
recipe: "blackarch"
source: "https://github.com/m4ll0k/WAScan"
license: "GPL3"
blackarch-pkgver: "37.6926338"
---

# Installing wascan

> Web Application Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wascan) (version `37.6926338`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S wascan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/m4ll0k/WAScan.git
cd WAScan
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[wascan]] · [[BlackArch Tools]]

