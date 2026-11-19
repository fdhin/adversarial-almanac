---
tool: "pdfbook-analyzer"
build-system: "scripts"
upstream: "http://sourceforge.net/projects/pdfbook/"
recipe: "blackarch"
source: "http://sourceforge.net/projects/pdfbook/"
license: "GPL2"
blackarch-pkgver: "2"
---

# Installing pdfbook-analyzer

> Utility for facebook memory forensics.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pdfbook-analyzer) (version `2`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S pdfbook-analyzer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone http://git.code.sf.net/p/pdfbook/code
cd code
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[pdfbook-analyzer]] · [[BlackArch Tools]]

