---
tool: "jsfuck"
build-system: "scripts"
upstream: "https://github.com/aemkei/jsfuck"
recipe: "blackarch"
source: "https://github.com/aemkei/jsfuck"
license: "custom:WTFPL"
blackarch-pkgver: "242.6f8fdf3"
---

# Installing jsfuck

> Write any JavaScript with 6 Characters: []()!+.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jsfuck) (version `242.6f8fdf3`, license custom:WTFPL).

## Arch / BlackArch

```bash
sudo pacman -S jsfuck    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/aemkei/jsfuck) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `php`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[jsfuck]] · [[BlackArch Tools]]

