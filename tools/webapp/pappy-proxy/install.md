---
tool: "pappy-proxy"
build-system: "scripts"
upstream: "https://github.com/roglew/pappy-proxy"
recipe: "blackarch"
source: "https://github.com/roglew/pappy-proxy"
license: "MIT"
blackarch-pkgver: "77.e1bb049"
---

# Installing pappy-proxy

> An intercepting proxy for web application testing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pappy-proxy) (version `77.e1bb049`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pappy-proxy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/roglew/pappy-proxy) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-apsw, python2-autobahn, python2-beautifulsoup4, python2-cmd2, python2-crochet, python2-decorator, python2-j2cli, python2-mock, python2-parsley, python2-pygments, python2-pyopenssl, python2-pysqlite, python2-pytest, python2-pytest-cov, python2-pytest-mock, python2-pytest-twisted, python2-scrypt, python2-service-identity, python2-setuptools, python2-simplejson, python2-twistedx, python2-txsocksx (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[pappy-proxy]] · [[BlackArch Tools]]

