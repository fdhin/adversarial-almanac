---
tool: "conpot"
build-system: "python"
upstream: "https://pypi.org/project/Conpot/"
recipe: "blackarch"
source: "https://pypi.org/project/Conpot/"
license: "GPL2"
blackarch-pkgver: "0.6.0"
---

# Installing conpot

> ICS honeypot with the goal to collect intelligence about the motives and methods of adversaries targeting industrial control systems.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/conpot) (version `0.6.0`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S conpot    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/Conpot/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `bottle`, `cpppo`, `crc16`, `crypto`, `cybox`, `distribute`, `enum-compat`, `freezegun`, `fs`, `gevent`, `jinja`, `libtaxii`, `lxml`, `mixbox`, `modbus-tk`, `natsort`, `pyghmi`, `pysmi`, `pysnmp`, `pytest`, `requests`, `scapy`, `setuptools`, `slugify`, `sphinx`, `stix`, `stix-validator`, `tftpy`, `tox`, `weakrefmethod`, `xlrd`
- **System libraries** (Arch name — translate for your distro):
  - `hpfeeds`
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[honeypot]] · [[conpot]] · [[BlackArch Tools]]

