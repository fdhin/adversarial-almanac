---
tool: "faradaysec"
build-system: "python"
upstream: "http://www.faradaysec.com/"
recipe: "blackarch"
source: "http://www.faradaysec.com/"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v3.19.0.r3228.gd00e81c"
---

# Installing faradaysec

> Collaborative Penetration Test and Vulnerability Management Platform.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/faradaysec) (version `v3.19.0.r3228.gd00e81c`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S faradaysec    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/infobyte/faraday.git
cd faraday
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `alembic`, `anyascii`, `apispec`, `apispec-webframeworks`, `autobahn`, `bcrypt`, `beautifulsoup4`, `click`, `colorama`, `couchdb`, `dateutil`, `deprecation`, `distro`, `email-validator`, `faraday-plugins`, `filedepot`, `filteralchemy-fork`, `flask`, `flask-classful`, `flask-kvsession-fork`, `flask-login`, `flask-restless`, `flask-security`, `flask-sqlalchemy`, `gobject`, `hypothesis`, `ipy`, `itsdangerous`, `lxml`, `marshmallow`, `marshmallow-sqlalchemy`, `mockito`, `msgpack`, `nplusone`, `pendulum`, `pgcli`, `pillow`, `pip`, `prompt_toolkit`, `psycopg2`, `pyasn1`, `pyopenssl`, `pysqlite3`, `pytest`, `pytz`, `requests`, `responses`, `selenium`, `service-identity`, `shodan`, `simplejson`, `simplekv`, `slugify`, `sphinx`, `sqlalchemy`, `syslog-rfc5424-formatter`, `tornado`, `tqdm`, `twisted`, `urllib3`, `w3af-api-client`, `webargs`, `websocket-client`, `werkzeug`, `whoosh`, `wtforms`
- **Build tools**: `git`, `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `couchdb`
  - `curl`
  - `gobject-introspection`
  - `gobject-introspection-runtime`
  - `gtk3` — Debian libgtk-3-dev / Fedora gtk3-devel
  - `libpqxx`
  - `postgresql`
  - `pyqt3`
  - `python`
  - `ruby`
  - `vte3`
  - `zsh`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[faradaysec]] · [[BlackArch Tools]]

