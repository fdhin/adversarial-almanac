---
tool: "pdfgrab"
build-system: "scripts"
upstream: "https://github.com/c0decave/pdfgrab"
recipe: "blackarch"
source: "https://github.com/c0decave/pdfgrab"
license: "GPL2"
blackarch-pkgver: "15.1327508"
---

# Installing pdfgrab

> Tool for searching pdfs withthin google and extracting pdf metadata.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pdfgrab) (version `15.1327508`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S pdfgrab    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/c0decave/pdfgrab) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `google`, `json2html`, `pycryptodome`, `pypdf2`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ipython`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[pdfgrab]] · [[BlackArch Tools]]

