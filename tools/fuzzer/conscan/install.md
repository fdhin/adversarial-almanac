---
tool: "conscan"
build-system: "scripts"
upstream: "http://nullsecurity.net/tools/scanner.html"
recipe: "blackarch"
source: "http://nullsecurity.net/tools/scanner.html"
license: "custom:unknown"
blackarch-pkgver: "1.2"
---

# Installing conscan

> A blackbox vulnerability scanner for the Concre5 CMS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/conscan) (version `1.2`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S conscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://nullsecurity.net/tools/scanner.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-lxml, python2-wget (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[conscan]] · [[BlackArch Tools]]

