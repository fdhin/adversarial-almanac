---
tool: "fuzzap"
build-system: "scripts"
upstream: "https://github.com/lostincynicism/FuzzAP"
recipe: "blackarch"
source: "https://github.com/lostincynicism/FuzzAP"
license: "custom"
blackarch-pkgver: "17.057002b"
---

# Installing fuzzap

> A python script for obfuscating wireless networks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fuzzap) (version `17.057002b`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S fuzzap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/lostincynicism/FuzzAP.git
cd FuzzAP
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[fuzzap]] · [[BlackArch Tools]]

