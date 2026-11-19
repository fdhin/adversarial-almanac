---
tool: "smuggler"
build-system: "scripts"
upstream: "https://github.com/defparam/smuggler"
recipe: "blackarch"
source: "https://github.com/defparam/smuggler"
license: "MIT"
blackarch-pkgver: "23.2be871e"
---

# Installing smuggler

> An HTTP Request Smuggling / Desync testing tool written in Python 3.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/smuggler) (version `23.2be871e`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S smuggler    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/defparam/smuggler) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[smuggler]] · [[BlackArch Tools]]

