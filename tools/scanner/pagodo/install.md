---
tool: "pagodo"
build-system: "scripts"
upstream: "https://github.com/opsdisk/pagodo"
recipe: "blackarch"
source: "https://github.com/opsdisk/pagodo"
license: "GPL3"
blackarch-pkgver: "166.3228879"
---

# Installing pagodo

> Google dork script to collect potentially vulnerable web pages and applications on the Internet.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pagodo) (version `166.3228879`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pagodo    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/opsdisk/pagodo) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `google`, `numpy`, `requests`, `yagooglesearch`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[pagodo]] · [[BlackArch Tools]]

