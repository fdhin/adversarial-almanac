---
tool: "androwarn"
build-system: "scripts"
upstream: "https://github.com/maaaaz/androwarn"
recipe: "blackarch"
source: "https://github.com/maaaaz/androwarn"
license: "GPL-3.0-or-later"
blackarch-pkgver: "135.626c02d"
---

# Installing androwarn

> Yet another static code analyzer for malicious Android applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/androwarn) (version `135.626c02d`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S androwarn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/maaaaz/androwarn) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `future`, `jinja`, `lxml`, `play-scraper`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `androguard`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[androwarn]] · [[BlackArch Tools]]

