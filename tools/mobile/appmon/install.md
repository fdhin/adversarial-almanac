---
tool: "appmon"
build-system: "scripts"
upstream: "https://github.com/dpnishant/appmon"
recipe: "blackarch"
source: "https://github.com/dpnishant/appmon"
license: "GPL-3.0-or-later"
blackarch-pkgver: "0.5.r36.gf753c4d"
---

# Installing appmon

> A runtime security testing & profiling framework for native apps on macOS, iOS & android and it is built using Frida.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/appmon) (version `0.5.r36.gf753c4d`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S appmon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/dpnishant/appmon) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `alembic`, `banal`, `chardet`, `click`, `dataset`, `dateutil`, `editor`, `flask`, `frida`, `htmlentities`, `itsdangerous`, `jinja`, `lxml`, `mako`, `markupsafe`, `normality`, `six`, `sqlalchemy`, `termcolor`, `werkzeug`, `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[appmon]] · [[BlackArch Tools]]

