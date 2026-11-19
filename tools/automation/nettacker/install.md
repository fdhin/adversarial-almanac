---
tool: "nettacker"
build-system: "python"
upstream: "https://github.com/OWASP/Nettacker"
recipe: "blackarch"
source: "https://github.com/OWASP/Nettacker"
license: "Apache-2.0"
blackarch-pkgver: "0.4.1.r17.g14ef2b4"
---

# Installing nettacker

> Automated Penetration Testing Framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nettacker) (version `0.4.1.r17.g14ef2b4`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S nettacker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/OWASP/Nettacker) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `aiohttp`, `backoff`, `bs4`, `build`, `censys`, `flask`, `installer`, `ipaddr`, `lockfile`, `mechanize`, `multiprocess`, `netaddr`, `numpy`, `paramiko`, `poetry`, `psycopg2`, `py3dns`, `pycurl`, `pyopenssl`, `pysocks`, `pyspf`, `requests`, `rule`, `setuptools`, `shodan`, `six`, `sqlalchemy`, `terminable-thread`, `texttable`, `wheel`, `win_inet_pton`, `xmltodict`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `canari`
  - `flake8`
  - `scapy`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[nettacker]] · [[BlackArch Tools]]

