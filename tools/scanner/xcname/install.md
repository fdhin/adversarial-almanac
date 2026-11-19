---
tool: "xcname"
build-system: "scripts"
upstream: "https://github.com/mandatoryprogrammer/xcname"
recipe: "blackarch"
source: "https://github.com/mandatoryprogrammer/xcname"
license: "custom:unknown"
blackarch-pkgver: "11.9c475a1"
---

# Installing xcname

> A tool for enumerating expired domains in CNAME records.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xcname) (version `11.9c475a1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S xcname    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/mandatoryprogrammer/xcname) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-ipwhois, python2-python-whois, python2-pywhois, python2-requests, python2-tldextract (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[xcname]] · [[BlackArch Tools]]

