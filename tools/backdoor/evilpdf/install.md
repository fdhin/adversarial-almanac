---
tool: "evilpdf"
build-system: "scripts"
upstream: "https://github.com/thelinuxchoice/evilpdf"
recipe: "blackarch"
source: "https://github.com/thelinuxchoice/evilpdf"
license: "GPL3"
blackarch-pkgver: "5.43696a8"
---

# Installing evilpdf

> Embedding executable files in PDF Documents.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/evilpdf) (version `5.43696a8`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S evilpdf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/thelinuxchoice/evilpdf) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pypdf2`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[evilpdf]] · [[BlackArch Tools]]

