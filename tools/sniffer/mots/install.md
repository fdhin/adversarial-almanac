---
tool: "mots"
build-system: "scripts"
upstream: "https://github.com/kevinkoo001/MotS"
recipe: "blackarch"
source: "https://github.com/kevinkoo001/MotS"
license: "custom:unknown"
blackarch-pkgver: "5.34017ca"
---

# Installing mots

> Man on the Side Attack - experimental packet injection and detection.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mots) (version `5.34017ca`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S mots    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/kevinkoo001/MotS.git
cd MotS
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-netifaces, python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[mots]] · [[BlackArch Tools]]

