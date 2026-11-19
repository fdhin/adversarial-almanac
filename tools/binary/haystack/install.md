---
tool: "haystack"
build-system: "scripts"
upstream: "https://github.com/trolldbois/python-haystack"
recipe: "blackarch"
aur-package: "haystack"
source: "https://github.com/trolldbois/python-haystack.git"
license: "GPL3"
blackarch-pkgver: "1823.c178b5a"
---

# Installing haystack

> A Python framework for finding C structures from process memory - heap analysis - Memory structures forensics.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/haystack) (version `1823.c178b5a`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S haystack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/trolldbois/python-haystack.git
cd python-haystack
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-levenshtein, python2-networkx, python2-ptrace, python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[haystack]] · [[BlackArch Tools]]

