---
tool: "web2ldap"
build-system: "python"
upstream: "https://web2ldap.de/"
recipe: "blackarch"
source: "https://web2ldap.de/"
license: "Apache-2.0"
blackarch-pkgver: "1.8.1"
---

# Installing web2ldap

> Full-featured LDAP client running as web application.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/web2ldap) (version `1.8.1`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S web2ldap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://web2ldap.de/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `asn1crypto`, `dnspython`, `iso3166`, `ldap0`, `paramiko`, `pip`, `setuptools`, `wheel`, `xlwt`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[web2ldap]] · [[BlackArch Tools]]

