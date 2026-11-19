---
tool: "eaphammer"
build-system: "scripts"
upstream: "https://github.com/s0lst1c3/eaphammer"
recipe: "blackarch"
source: "https://github.com/s0lst1c3/eaphammer"
license: "GPL3"
blackarch-pkgver: "v1.14.1.r0.g91e8956"
---

# Installing eaphammer

> Targeted evil twin attacks against WPA2-Enterprise networks. Indirect wireless pivots using hostile portal attacks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/eaphammer) (version `v1.14.1.r0.g91e8956`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S eaphammer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/s0lst1c3/eaphammer) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `flask`, `flask-cors`, `flask-socketio`, `gevent`, `lxml`, `pem`, `pyopenssl`, `pywebcopy`, `requests`, `tqdm`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `apache`
  - `curl`
  - `dnsmasq`
  - `hcxdumptool`
  - `hcxtools`
  - `libnfnetlink`
  - `libnl`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `python`
  - `responder`
  - `scapy`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[eaphammer]] · [[BlackArch Tools]]

