---
tool: "harpoon"
build-system: "scripts"
upstream: "https://github.com/Te-k/harpoon"
recipe: "blackarch"
source: "https://github.com/Te-k/harpoon"
license: "GPL3"
blackarch-pkgver: "388.4bb8250"
---

# Installing harpoon

> CLI tool for open source and threat intelligence.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/harpoon) (version `388.4bb8250`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S harpoon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Te-k/harpoon) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `virtualenv`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libxml2` — Debian libxml2-dev / Fedora libxml2-devel
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[harpoon]] · [[BlackArch Tools]]

