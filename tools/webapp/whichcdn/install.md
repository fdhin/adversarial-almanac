---
tool: "whichcdn"
build-system: "scripts"
upstream: "https://github.com/Nitr4x/whichCDN"
recipe: "blackarch"
source: "https://github.com/Nitr4x/whichCDN"
license: "MIT"
blackarch-pkgver: "22.5fc6ddd"
---

# Installing whichcdn

> Tool to detect if a given website is protected by a Content Delivery Network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/whichcdn) (version `22.5fc6ddd`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S whichcdn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Nitr4x/whichCDN.git
cd whichCDN
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[whichcdn]] · [[BlackArch Tools]]

