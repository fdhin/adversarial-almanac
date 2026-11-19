---
tool: "multiinjector"
build-system: "scripts"
upstream: "http://chaptersinwebsecurity.blogspot.de/2008/11/multiinjector-v03-released.html"
recipe: "blackarch"
source: "http://chaptersinwebsecurity.blogspot.de/2008/11/multiinjector-v03-released.html"
license: "GPL"
blackarch-pkgver: "0.4"
---

# Installing multiinjector

> Automatic SQL injection utility using a lsit of URI addresses to test parameter manipulation.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/multiinjector) (version `0.4`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S multiinjector    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://chaptersinwebsecurity.blogspot.de/2008/11/multiinjector-v03-released.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pycurl (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[multiinjector]] · [[BlackArch Tools]]

