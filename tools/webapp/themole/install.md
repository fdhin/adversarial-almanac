---
tool: "themole"
build-system: "scripts"
upstream: "http://sourceforge.net/projects/themole/"
recipe: "blackarch"
source: "http://sourceforge.net/projects/themole/"
license: "GPL3"
blackarch-pkgver: "0.3"
---

# Installing themole

> Automatic SQL injection exploitation tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/themole) (version `0.3`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S themole    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/themole/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `lxml`
- **System libraries** (Arch name — translate for your distro):
  - `python3`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[themole]] · [[BlackArch Tools]]

