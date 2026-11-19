---
tool: "jok3r"
build-system: "scripts"
upstream: "https://github.com/koutto/jok3r"
recipe: "blackarch"
source: "https://github.com/koutto/jok3r"
license: "GPL-3.0-or-later"
blackarch-pkgver: "447.0761996"
---

# Installing jok3r

> Network and Web Pentest Framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jok3r) (version `447.0761996`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S jok3r    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/koutto/jok3r) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `ansi2html`, `beautifulsoup4`, `blessed`, `cmd2`, `colorama`, `colored`, `colorlog`, `enlighten`, `humanfriendly`, `pillow`, `prettytable`, `python-libnmap`, `regex`, `requests`, `selenium`, `shodan`, `six`, `sqlalchemy`, `sqlalchemy-utils`, `tld`, `urllib3`
- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[jok3r]] · [[BlackArch Tools]]

