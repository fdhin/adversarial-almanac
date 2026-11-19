---
tool: "golismero"
build-system: "scripts"
upstream: "https://github.com/golismero/golismero"
recipe: "blackarch"
source: "https://github.com/golismero/golismero"
license: "custom"
blackarch-pkgver: "73.7d605b9"
---

# Installing golismero

> Opensource web security testing framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/golismero) (version `73.7d605b9`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S golismero    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/golismero/golismero) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argcomplete, python2-argparse, python2-beautifulsoup3, python2-cjson, python2-colorizer, python2-decorator, python2-django, python2-dnspython, python2-docutils, python2-netaddr, python2-nltk, python2-psycopg2, python2-pycurl, python2-pymongo, python2-pysqlite, python2-requests, python2-requests-ntlm, python2-simplejson, python2-texttable, python2-tldextract, python2-yaml (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[golismero]] · [[BlackArch Tools]]

