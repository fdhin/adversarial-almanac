---
tool: "ufonet"
build-system: "scripts"
upstream: "https://github.com/epsylon/ufonet"
recipe: "blackarch"
source: "https://github.com/epsylon/ufonet"
license: "custom:unknown"
blackarch-pkgver: "89.9656627"
---

# Installing ufonet

> A tool designed to launch DDoS attacks against a target, using 'Open Redirect' vectors on third party web applications, like botnet.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ufonet) (version `89.9656627`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ufonet    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/epsylon/ufonet) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `crypto`, `geoip`, `pycryptodome`, `pycryptodomex`, `pycurl`, `pygeoip`, `python-geoip`, `requests`, `scapy`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `geoip`
  - `python`
  - `scapy`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[dos]] · [[ufonet]] · [[BlackArch Tools]]

