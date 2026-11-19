---
tool: "python2-ldapdomaindump"
build-system: "python"
upstream: "https://pypi.org/project/ldapdomaindump/#files"
recipe: "blackarch"
source: "https://pypi.org/project/ldapdomaindump/#files"
license: "MIT"
blackarch-pkgver: "0.9.4"
---

# Installing python2-ldapdomaindump

> Active Directory information dumper via LDAP.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/python2-ldapdomaindump) (version `0.9.4`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S python2-ldapdomaindump    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/ldapdomaindump/#files) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-dnspython, python2-future, python2-ldap3, python2-setuptools (legacy; needs python2)

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  python2 setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[python2-ldapdomaindump]] · [[BlackArch Tools]]

