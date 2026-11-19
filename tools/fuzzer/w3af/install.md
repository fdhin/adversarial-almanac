---
tool: "w3af"
build-system: "scripts"
upstream: "https://github.com/andresriancho/w3af/releases"
recipe: "blackarch"
source: "https://github.com/andresriancho/w3af/releases"
license: "GPL"
blackarch-pkgver: "1.6.49"
---

# Installing w3af

> Web Application Attack and Audit Framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/w3af) (version `1.6.49`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S w3af    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/andresriancho/w3af/releases) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `graphviz`
  - `gtk2`
  - `halberd`
  - `libxml2` — Debian libxml2-dev / Fedora libxml2-devel
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `libyaml`
  - `pygtk`
  - `pygtksourceview2`
  - `python2`
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel
- ⚠️ Python 2 only deps: python2-beautifulsoup3, python2-chardet, python2-clamd, python2-cluster, python2-darts.util.lru, python2-esmre, python2-future, python2-futures, python2-gitpython, python2-guess-language, python2-jinja, python2-lxml, python2-markdown, python2-msgpack, python2-nltk, python2-ntlm, python2-pdfminer, python2-phply, python2-pip, python2-pyasn1, python2-pybloomfiltermmap, python2-pygithub, python2-pyopenssl, python2-pysqlite, python2-scapy, python2-stopit, python2-tblib, python2-vulndb, python2-xdot, python2-yaml (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/applications`

Backlinks: [[fuzzer]] · [[w3af]] · [[BlackArch Tools]]

