---
tool: "yeti"
build-system: "scripts"
upstream: "https://github.com/yeti-platform/yeti"
recipe: "blackarch"
source: "https://github.com/yeti-platform/yeti"
license: "Apache"
blackarch-pkgver: "3571.40fe769b"
---

# Installing yeti

> A platform meant to organize observables, indicators of compromise, TTPs, and knowledge on threats in a single, unified repository.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/yeti) (version `3571.40fe769b`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S yeti    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/yeti-platform/yeti) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `celery`, `chardet`, `click`, `click-plugins`, `colorama`, `cssselect`, `dateutil`, `dnspython`, `docutils`, `flask`, `flask-api`, `flask-classy`, `flask-login`, `flask-mongoengine`, `flask-negotiation`, `flask-wtf`, `future`, `geoip2`, `html2text`, `idna`, `imagesize`, `ipaddr`, `iptools`, `ipwhois`, `itsdangerous`, `jinja`, `kombu`, `libmagic`, `lxml`, `maclookup`, `magic`, `markupsafe`, `maxminddb`, `mongoengine`, `packaging`, `pandas`, `pdfkit`, `pdfminer`, `psutil`, `pygments`, `pymongo`, `pyopenssl`, `pyparsing`, `pysocks`, `python-whois`, `pytz`, `readability-lxml`, `redis`, `requests-file`, `shodan`, `simplejson`, `six`, `snowballstemmer`, `sphinx`, `sphinx_rtd_theme`, `sphinxcontrib-httpdomain`, `sphinxcontrib-websupport`, `tldextract`, `typing`, `url-normalize`, `urllib3`, `urlnorm`, `vine`, `werkzeug`, `wtforms`, `xlsxwriter`, `yara-python-dex`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libxml2` — Debian libxml2-dev / Fedora libxml2-devel
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `mongodb`
  - `python`
  - `redis`
  - `wkhtmltopdf`
  - `yarn`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[yeti]] · [[BlackArch Tools]]

