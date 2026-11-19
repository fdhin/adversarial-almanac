---
tool: "datasploit"
build-system: "scripts"
upstream: "https://github.com/upgoingstar/datasploit"
recipe: "blackarch"
source: "https://github.com/upgoingstar/datasploit"
license: "GPL3"
blackarch-pkgver: "369.28d9caf"
---

# Installing datasploit

> Performs automated OSINT and more.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/datasploit) (version `369.28d9caf`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S datasploit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/upgoingstar/datasploit) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-anyjson, python2-beautifulsoup4, python2-billiard, python2-celery, python2-clearbit, python2-configobj, python2-django, python2-dnspython, python2-future, python2-html5lib, python2-idna, python2-ipwhois, python2-json2html, python2-netaddr, python2-praw, python2-pymongo, python2-python-wappalyzer, python2-python-whois, python2-pytz, python2-requests, python2-requests-file, python2-shodan, python2-termcolor, python2-tweepy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[datasploit]] · [[BlackArch Tools]]

