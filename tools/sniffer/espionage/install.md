---
tool: "espionage"
build-system: "scripts"
upstream: "https://github.com/josh0xA/Espionage"
recipe: "blackarch"
aur-package: "espionage"
source: "https://github.com/josh0xA/Espionage"
license: "MIT"
blackarch-pkgver: "48.2d57edd"
---

# Installing espionage

> A Network Packet and Traffic Interceptor For Linux. Sniff All Data Sent Through a Network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/espionage) (version `48.2d57edd`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S espionage    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/josh0xA/Espionage.git
cd Espionage
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `httpcap`, `netifaces`, `requests`, `scapy`, `termcolor`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[espionage]] · [[BlackArch Tools]]

