---
tool: "whapa"
build-system: "scripts"
upstream: "https://github.com/B16f00t/whapa"
recipe: "blackarch"
source: "https://github.com/B16f00t/whapa"
license: "GPL3"
blackarch-pkgver: "394.c17ca34"
---

# Installing whapa

> WhatsApp Parser Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/whapa) (version `394.c17ca34`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S whapa    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/B16f00t/whapa) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `gpsoauth`, `pycryptodome`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `tk`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[whapa]] · [[BlackArch Tools]]

