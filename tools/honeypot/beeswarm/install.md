---
tool: "beeswarm"
build-system: "scripts"
upstream: "https://github.com/honeynet/beeswarm/"
recipe: "blackarch"
source: "https://github.com/honeynet/beeswarm/"
license: "GPL3"
blackarch-pkgver: "1183.db51ea0"
---

# Installing beeswarm

> Honeypot deployment made easy.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/beeswarm) (version `1183.db51ea0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S beeswarm    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/honeynet/beeswarm/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-backports-os, python2-cssselect, python2-ecdsa, python2-enum34, python2-flask, python2-flask-login, python2-flask-wtf, python2-fs, python2-future, python2-gevent, python2-lxml, python2-mock, python2-netifaces, python2-nose, python2-ntplib, python2-paramiko, python2-pycryptodome, python2-pydes, python2-pyopenssl, python2-pysendfile, python2-pyzmq, python2-requests, python2-sqlalchemy, python2-telnetsrv, python2-tornado, python2-typing (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[honeypot]] · [[beeswarm]] · [[BlackArch Tools]]

