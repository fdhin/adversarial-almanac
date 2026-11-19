---
tool: "allthevhosts"
build-system: "scripts"
upstream: "http://labs.portcullis.co.uk/tools/finding-all-the-vhosts/"
recipe: "blackarch"
source: "http://labs.portcullis.co.uk/tools/finding-all-the-vhosts/"
license: "GPL"
blackarch-pkgver: "1.0"
---

# Installing allthevhosts

> A vhost discovery tool that scrapes various web applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/allthevhosts) (version `1.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S allthevhosts    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://labs.portcullis.co.uk/tools/finding-all-the-vhosts/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[allthevhosts]] · [[BlackArch Tools]]

