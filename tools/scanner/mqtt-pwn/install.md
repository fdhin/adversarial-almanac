---
tool: "mqtt-pwn"
build-system: "scripts"
upstream: "https://github.com/akamai-threat-research/mqtt-pwn"
recipe: "blackarch"
source: "https://github.com/akamai-threat-research/mqtt-pwn"
license: "GPL3"
blackarch-pkgver: "43.40368e5"
---

# Installing mqtt-pwn

> A one-stop-shop for IoT Broker penetration-testing and security assessment operations.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mqtt-pwn) (version `43.40368e5`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S mqtt-pwn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/akamai-threat-research/mqtt-pwn) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `blessings`, `cmd2`, `paho-mqtt`, `peewee`, `prettytable`, `pygments`, `shodan`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[mqtt-pwn]] · [[BlackArch Tools]]

