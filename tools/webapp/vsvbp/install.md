---
tool: "vsvbp"
build-system: "scripts"
upstream: "https://github.com/varunjammula/VSVBP"
recipe: "blackarch"
source: "https://github.com/varunjammula/VSVBP"
license: "custom:unknown"
blackarch-pkgver: "6.241a7ab"
---

# Installing vsvbp

> Black box tool for Vulnerability detection in web applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vsvbp) (version `6.241a7ab`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S vsvbp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/varunjammula/VSVBP.git
cd VSVBP
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup3, python2-beautifulsoup4, python2-coverage, python2-flask, python2-flask-babel, python2-flask-login, python2-flask-mail, python2-flask-openid, python2-flask-sqlalchemy, python2-flask-whooshalchemy-redux, python2-flask-wtf, python2-flipflop, python2-guess-language, python2-html2text, python2-lxml, python2-mechanize, python2-pysocks, python2-tldextract, python2-wtforms (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[vsvbp]] · [[BlackArch Tools]]

