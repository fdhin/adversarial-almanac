---
tool: "wifi-pumpkin"
build-system: "python"
upstream: "https://github.com/P0cL4bs/wifipumpkin3"
recipe: "blackarch"
source: "https://github.com/P0cL4bs/wifipumpkin3"
license: "MIT"
blackarch-pkgver: "v1.1.7.r2.g344a475"
---

# Installing wifi-pumpkin

> Framework for Rogue Wi-Fi Access Point Attack.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wifi-pumpkin) (version `v1.1.7.r2.g344a475`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S wifi-pumpkin    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/P0cL4bs/wifipumpkin3) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `aiofiles`, `asn1crypto`, `beautifulsoup4`, `beautifultable`, `bs4`, `coverage`, `dhcplib`, `dnslib`, `dnspython`, `flask`, `flask-restful`, `isc_dhcp_leases`, `loguru`, `lxml`, `netaddr`, `netifaces`, `ping3`, `pip`, `pyjwt`, `pyopenssl`, `pyqt5`, `pyqt5-sip`, `scapy`, `scapy-http`, `setuptools`, `tabulate`, `termcolor`, `twisted`, `urwid`, `wcwidth`, `werkzeug`, `wheel`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `dhcp`
  - `networkmanager`
  - `python`
  - `responder`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[wifi-pumpkin]] · [[BlackArch Tools]]

