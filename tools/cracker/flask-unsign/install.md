---
tool: "flask-unsign"
build-system: "python"
upstream: "https://github.com/Paradoxis/Flask-Unsign"
recipe: "blackarch"
nix-package: "flask-unsign"
aur-package: "flask-unsign"
source: "https://github.com/Paradoxis/Flask-Unsign"
license: "MIT"
blackarch-pkgver: "v1.2.1.r0.g6e6e91d"
---

# Installing flask-unsign

> Command line tool to fetch, decode, brute-force and craft session cookies of a Flask application by guessing secret keys.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/flask-unsign) (version `v1.2.1.r0.g6e6e91d`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S flask-unsign    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Paradoxis/Flask-Unsign.git
cd Flask-Unsign
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `flask`, `itsdangerous`, `markupsafe`, `requests`, `setuptools`, `werkzeug`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[flask-unsign]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#flask-unsign    # ad-hoc run, no install
nix profile install nixpkgs#flask-unsign   # or persist it
```
