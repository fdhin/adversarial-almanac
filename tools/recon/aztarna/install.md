---
tool: "aztarna"
build-system: "python"
upstream: "https://github.com/aliasrobotics/aztarna"
recipe: "blackarch"
source: "https://github.com/aliasrobotics/aztarna"
license: "GPL3"
blackarch-pkgver: "1.2.1"
---

# Installing aztarna

> A footprinting tool for ROS and SROS systems.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/aztarna) (version `1.2.1`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S aztarna    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/aliasrobotics/aztarna) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `aiohttp`, `aiohttp-xmlrpc`, `argcomplete`, `asn1crypto`, `async-timeout`, `attrs`, `babel`, `certifi`, `cffi`, `chardet`, `click`, `click-plugins`, `colorama`, `cryptography`, `dnspython`, `docutils`, `idna`, `idna_ssl`, `imagesize`, `ipwhois`, `jinja`, `lxml`, `markupsafe`, `multidict`, `packaging`, `property`, `pycparser`, `pygments`, `pyparsing`, `pytz`, `requests`, `scapy`, `setuptools`, `shodan`, `six`, `snowballstemmer`, `sphinx`, `sphinxcontrib-websupport`, `urllib3`, `uvloop`, `xlsxwriter`, `yarl`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[aztarna]] · [[BlackArch Tools]]

