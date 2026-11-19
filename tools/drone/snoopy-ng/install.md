---
tool: "snoopy-ng"
build-system: "scripts"
upstream: "https://github.com/sensepost/snoopy-ng"
recipe: "blackarch"
source: "https://github.com/sensepost/snoopy-ng"
license: "custom"
blackarch-pkgver: "128.eac73f5"
provides: ["snoopy", "snoopy-auth", "sslstrip-snoopy"]
---

# Installing snoopy-ng

> A distributed, sensor, data collection, interception, analysis, and visualization framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/snoopy-ng) (version `128.eac73f5`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S snoopy-ng    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/sensepost/snoopy-ng) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `aircrack-ng`
  - `autossh`
  - `gpsd`
  - `iw`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `mitmproxy`
  - `ppp`
  - `python2`
  - `sqlite3`
  - `tcpdump`
- ⚠️ Python 2 only deps: python2-dateutil, python2-dnslib, python2-dpkt, python2-flask, python2-httplib2, python2-netifaces, python2-psutil, python2-publicsuffix, python2-pybluez, python2-pyinotify, python2-pylibpcap, python2-pyserial, python2-requests, python2-smspdu, python2-sqlalchemy (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/snoopy`, `/usr/bin/snoopy-auth`, `/usr/bin/sslstrip-snoopy`
- Data: `/usr/share/snoopy-ng`, `/usr/share/sslstrip-snoopy`

Backlinks: [[drone]] · [[snoopy-ng]] · [[BlackArch Tools]]

