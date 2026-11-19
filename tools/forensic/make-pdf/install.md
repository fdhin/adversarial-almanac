---
tool: "make-pdf"
build-system: "scripts"
upstream: "http://blog.didierstevens.com/programs/pdf-tools/"
recipe: "blackarch"
source: "http://blog.didierstevens.com/programs/pdf-tools/"
license: "GPL"
blackarch-pkgver: "0.1.7"
---

# Installing make-pdf

> This tool will embed javascript inside a PDF document.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/make-pdf) (version `0.1.7`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S make-pdf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://blog.didierstevens.com/programs/pdf-tools/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `dos2unix`
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[make-pdf]] · [[BlackArch Tools]]

