---
tool: "fav-up"
build-system: "scripts"
upstream: "https://github.com/pielco11/fav-up"
recipe: "blackarch"
source: "https://github.com/pielco11/fav-up"
license: "MIT"
blackarch-pkgver: "58.5ce68a8"
---

# Installing fav-up

> IP lookup by favicon using Shodan.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fav-up) (version `58.5ce68a8`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S fav-up    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/pielco11/fav-up) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `fake-useragent`, `ipwhois`, `mmh3`, `requests`, `shodan`, `tqdm`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[fav-up]] · [[BlackArch Tools]]

