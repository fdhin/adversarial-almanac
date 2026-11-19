---
tool: "phonia"
build-system: "scripts"
upstream: "https://github.com/entynetproject/phonia"
recipe: "blackarch"
source: "https://github.com/entynetproject/phonia"
license: "MIT"
blackarch-pkgver: "593.8ae14ff"
---

# Installing phonia

> Advanced toolkits to scan phone numbers using only free resources.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/phonia) (version `593.8ae14ff`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S phonia    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/entynetproject/phonia) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `colorama`, `config`, `html5lib`, `phonenumbers`, `requests`, `selenium`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `geckodriver`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[phonia]] · [[BlackArch Tools]]

