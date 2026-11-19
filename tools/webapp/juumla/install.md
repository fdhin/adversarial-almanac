---
tool: "juumla"
build-system: "scripts"
upstream: "https://github.com/oppsec/juumla"
recipe: "blackarch"
source: "https://github.com/oppsec/juumla"
license: "MIT"
blackarch-pkgver: "114.4c6f746"
---

# Installing juumla

> Python tool created to identify Joomla version, scan for vulnerabilities and search for config files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/juumla) (version `114.4c6f746`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S juumla    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/oppsec/juumla) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`, `rich`, `xmltodict`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[juumla]] · [[BlackArch Tools]]

