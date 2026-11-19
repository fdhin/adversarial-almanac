---
tool: "hexorbase"
build-system: "scripts"
upstream: "https://code.google.com/p/hexorbase/"
recipe: "blackarch"
source: "https://web.archive.org/web/20220425002001mp_/https://github.com/savio-code/hexorbase/archive/refs/heads/master.zip"
license: "GPL-3.0-or-later"
blackarch-pkgver: "7"
---

# Installing hexorbase

> A database application designed for administering and auditing multiple database servers simultaneously from a centralized location. It is capable of performing SQL queries and bruteforce attacks against common database servers (MySQL, SQLite, Microsoft SQL Server, Oracle, PostgreSQL).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hexorbase) (version `7`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S hexorbase    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://web.archive.org/web/20220425002001mp_/https://github.com/savio-code/hexorbase/archive/refs/heads/master.zip
cd master.zip
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `mysql-python`
  - `pymssql`
  - `python2`
- ⚠️ Python 2 only deps: python2-psycopg2, python2-pyqt4, python2-qscintilla (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[hexorbase]] · [[BlackArch Tools]]

