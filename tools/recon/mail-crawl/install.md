---
tool: "mail-crawl"
build-system: "scripts"
upstream: "https://raw.githubusercontent.com/galkan/tools/master/mail-crawl/"
recipe: "blackarch"
source: "https://raw.githubusercontent.com/galkan/tools/master/mail-crawl/mail-crawl.py"
license: "custom"
blackarch-pkgver: "0.1"
---

# Installing mail-crawl

> Tool to harvest emails from website.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mail-crawl) (version `0.1`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S mail-crawl    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://raw.githubusercontent.com/galkan/tools/master/mail-crawl/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[mail-crawl]] · [[BlackArch Tools]]

