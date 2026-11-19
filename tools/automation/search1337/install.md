---
tool: "search1337"
build-system: "scripts"
upstream: "https://github.com/b3mb4m/Search1337"
recipe: "blackarch"
source: "https://github.com/b3mb4m/Search1337"
license: "MIT"
blackarch-pkgver: "11.bf03ec9"
---

# Installing search1337

> 1337Day Online Exploit Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/search1337) (version `11.bf03ec9`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S search1337    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/idkwim/Search1337.git
cd Search1337
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-mechanize, python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[search1337]] · [[BlackArch Tools]]

