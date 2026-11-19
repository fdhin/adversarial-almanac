---
tool: "hubbit-sniffer"
build-system: "scripts"
upstream: "https://github.com/cthit/hubbIT-sniffer"
recipe: "blackarch"
source: "https://github.com/cthit/hubbIT-sniffer"
license: "GPL3"
blackarch-pkgver: "74.460ecf8"
---

# Installing hubbit-sniffer

> Simple application that listens for WIFI-frames and records the mac-address of the sender and posts them to a REST-api.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hubbit-sniffer) (version `74.460ecf8`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S hubbit-sniffer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/cthit/hubbIT-sniffer.git
cd hubbIT-sniffer
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[hubbit-sniffer]] · [[BlackArch Tools]]

