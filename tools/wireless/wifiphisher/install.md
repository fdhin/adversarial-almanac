---
tool: "wifiphisher"
build-system: "python"
upstream: "https://github.com/wifiphisher/wifiphisher"
recipe: "blackarch"
source: "https://github.com/wifiphisher/wifiphisher"
license: "MIT"
blackarch-pkgver: "800.4ae3365"
---

# Installing wifiphisher

> Fast automated phishing attacks against WPA networks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wifiphisher) (version `800.4ae3365`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S wifiphisher    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/wifiphisher/wifiphisher) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `blessings`, `dbus`, `httplib2`, `pbkdf2`, `pyopenssl`, `pyric`, `scapy`, `setuptools`, `tornado`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `aircrack-ng`
  - `dnsmasq`
  - `hostapd`
  - `python`
  - `roguehostapd`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[wifiphisher]] · [[BlackArch Tools]]

