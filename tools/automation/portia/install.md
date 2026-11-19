---
tool: "portia"
build-system: "scripts"
upstream: "https://github.com/SpiderLabs/portia"
recipe: "blackarch"
source: "https://github.com/SpiderLabs/portia"
license: "Apache"
blackarch-pkgver: "39.2e6e608"
---

# Installing portia

> Automate a number of techniques commonly performed on internal network penetration tests after a low privileged account has been compromised.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/portia) (version `39.2e6e608`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S portia    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/SpiderLabs/portia) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ntdsxtract`
  - `pymssql`
  - `python2`
  - `volatility3`
- ⚠️ Python 2 only deps: python2-argparse, python2-dnspython, python2-impacket, python2-netaddr, python2-pyasn1, python2-pymysql, python2-pyopenssl, python2-pysmb, python2-python-nmap, python2-setuptools, python2-tabulate, python2-termcolor, python2-xmltodict (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[portia]] · [[BlackArch Tools]]

