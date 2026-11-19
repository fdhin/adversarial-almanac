---
tool: "netzob"
build-system: "python"
upstream: "https://github.com/netzob/netzob/"
recipe: "blackarch"
aur-package: "netzob"
source: "https://github.com/netzob/netzob/"
license: "GPL3"
blackarch-pkgver: "1.0.2"
---

# Installing netzob

> An open source tool for reverse engineering, traffic generation and fuzzing of communication protocols.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netzob) (version `1.0.2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S netzob    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/netzob/netzob/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `arpreq`, `bintrees`, `bitarray`, `colorama`, `flask`, `gobject`, `jsonpickle`, `lxml`, `mock`, `netaddr`, `numpy`, `pcapy`, `pylstar`, `setuptools`, `werkzeug`
- **System libraries** (Arch name — translate for your distro):
  - `graphviz`
  - `gtk3` — Debian libgtk-3-dev / Fedora gtk3-devel
  - `impacket`
  - `libxml2` — Debian libxml2-dev / Fedora libxml2-devel
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-Release-netzob-$pkgver/$pkgname"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[reversing]] · [[netzob]] · [[BlackArch Tools]]

