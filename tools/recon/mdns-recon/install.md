---
tool: "mdns-recon"
build-system: "scripts"
upstream: "https://github.com/chadillac/mdns_recon"
recipe: "blackarch"
source: "https://github.com/chadillac/mdns_recon"
license: "MIT"
blackarch-pkgver: "11.69b864e"
---

# Installing mdns-recon

> An mDNS recon tool written in Python.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mdns-recon) (version `11.69b864e`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S mdns-recon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/chadillac/mdns_recon.git
cd mdns_recon
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[mdns-recon]] · [[BlackArch Tools]]

