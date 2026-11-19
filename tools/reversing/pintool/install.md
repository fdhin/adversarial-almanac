---
tool: "pintool"
build-system: "scripts"
upstream: "https://github.com/wagiro/pintool"
recipe: "blackarch"
source: "https://github.com/wagiro/pintool"
license: "MIT"
blackarch-pkgver: "24.d538a79"
---

# Installing pintool

> This tool can be useful for solving some reversing challenges in CTFs events.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pintool) (version `24.d538a79`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pintool    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/wagiro/pintool) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[reversing]] · [[pintool]] · [[BlackArch Tools]]

