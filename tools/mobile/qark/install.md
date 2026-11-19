---
tool: "qark"
build-system: "scripts"
upstream: "https://github.com/linkedin/qark"
recipe: "blackarch"
source: "https://github.com/linkedin/qark"
license: "custom:unknown"
blackarch-pkgver: "301.ba1b265"
---

# Installing qark

> Tool to look for several security related Android application vulnerabilities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/qark) (version `301.ba1b265`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S qark    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/linkedin/qark) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-asn1crypto, python2-certifi, python2-cffi, python2-chardet, python2-click, python2-cryptography, python2-enum34, python2-idna, python2-ipaddress, python2-javalang, python2-jinja, python2-markupsafe, python2-pluginbase, python2-pycparser, python2-pyopenssl, python2-requests, python2-setuptools, python2-six, python2-urllib3 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[qark]] · [[BlackArch Tools]]

