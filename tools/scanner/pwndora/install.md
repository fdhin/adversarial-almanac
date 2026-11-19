---
tool: "pwndora"
build-system: "scripts"
upstream: "https://github.com/alechilczenko/pwndora"
recipe: "blackarch"
source: "https://github.com/alechilczenko/pwndora"
license: "Apache-2.0"
blackarch-pkgver: "1.ba7f6e6"
---

# Installing pwndora

> Massive IPv4 scanner, find and analyze internet-connected devices in minutes, create your own IoT search engine at home.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pwndora) (version `1.ba7f6e6`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S pwndora    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/alechilczenko/pwndora) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `colorama`, `geoip2`, `loguru`, `pycountry`, `pyfiglet`, `pymongo`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `webtech`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[pwndora]] · [[BlackArch Tools]]

