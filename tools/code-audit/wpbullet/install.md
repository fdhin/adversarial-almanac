---
tool: "wpbullet"
build-system: "scripts"
upstream: "https://github.com/webarx-security/wpbullet"
recipe: "blackarch"
source: "https://github.com/webarx-security/wpbullet"
license: "GPL-2.0-or-later"
blackarch-pkgver: "34.6185112"
---

# Installing wpbullet

> A static code analysis for WordPress (and PHP).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wpbullet) (version `34.6185112`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S wpbullet    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/webarx-security/wpbullet) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `certifi`, `chardet`, `colorama`, `idna`, `requests`, `tabulate`, `terminaltables`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[wpbullet]] · [[BlackArch Tools]]

