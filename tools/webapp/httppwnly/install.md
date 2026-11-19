---
tool: "httppwnly"
build-system: "scripts"
upstream: "https://github.com/Danladi/HttpPwnly"
recipe: "blackarch"
source: "https://github.com/Danladi/HttpPwnly"
license: "custom:unknown"
blackarch-pkgver: "47.528a664"
---

# Installing httppwnly

> "Repeater" style XSS post-exploitation tool for mass browser control.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/httppwnly) (version `47.528a664`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S httppwnly    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Danladi/HttpPwnly.git
cd HttpPwnly
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-flask, python2-flask-bcrypt, python2-flask-cors, python2-flask-login, python2-flask-socketio, python2-flask-sqlalchemy, python2-pysqlite, python2-python-socketio (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[httppwnly]] · [[BlackArch Tools]]

