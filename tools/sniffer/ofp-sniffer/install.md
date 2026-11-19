---
tool: "ofp-sniffer"
build-system: "scripts"
upstream: "https://github.com/amlight/ofp_sniffer"
recipe: "blackarch"
source: "https://github.com/amlight/ofp_sniffer"
license: "Apache-2.0"
blackarch-pkgver: "1.0.0.r89.g0d907da"
---

# Installing ofp-sniffer

> An OpenFlow sniffer to help network troubleshooting in production networks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ofp-sniffer) (version `1.0.0.r89.g0d907da`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S ofp-sniffer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/amlight/ofp_sniffer.git
cd ofp_sniffer
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `dateutil`, `flask`, `hexdump`, `influxdb`, `pcapy`, `python-openflow`, `termcolor`, `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[ofp-sniffer]] · [[BlackArch Tools]]

