---
tool: "activedirectoryenum"
build-system: "python"
upstream: "https://github.com/CasperGN/ActiveDirectoryEnumeration"
recipe: "blackarch"
source: "https://github.com/CasperGN/ActiveDirectoryEnumeration"
license: "MIT"
blackarch-pkgver: "0.5.0"
---

# Installing activedirectoryenum

> Enumerate AD through LDAP.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/activedirectoryenum) (version `0.5.0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S activedirectoryenum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/CasperGN/ActiveDirectoryEnumeration) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `cffi`, `click`, `cryptography`, `dnspython`, `flask`, `future`, `itsdangerous`, `jinja`, `ldap3`, `ldapdomaindump`, `markupsafe`, `progressbar`, `pyasn1`, `pycparser`, `pycryptodomex`, `pyopenssl`, `setuptools`, `six`, `termcolor`, `werkzeug`
- **System libraries** (Arch name — translate for your distro):
  - `bloodhound-python`
  - `impacket`
  - `python`

## Exact build steps from the recipe

```bash
cd "$__pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[activedirectoryenum]] · [[BlackArch Tools]]

