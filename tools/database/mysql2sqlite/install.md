---
tool: "mysql2sqlite"
build-system: "scripts"
upstream: "https://gist.github.com/esperlu/943776"
recipe: "blackarch"
source: "https://gist.github.com/esperlu/943776"
license: "custom:unknown"
blackarch-pkgver: "14.e5b2c31"
---

# Installing mysql2sqlite

> Converts a mysqldump file into a Sqlite 3 compatible file.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mysql2sqlite) (version `14.e5b2c31`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S mysql2sqlite    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://gist.github.com/esperlu/943776) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `mariadb-clients`
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[database]] · [[mysql2sqlite]] · [[BlackArch Tools]]

