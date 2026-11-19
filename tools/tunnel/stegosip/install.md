---
tool: "stegosip"
build-system: "scripts"
upstream: "https://github.com/epinna/Stegosip"
recipe: "blackarch"
source: "https://github.com/epinna/Stegosip"
license: "custom:unknown"
blackarch-pkgver: "11.5cda6d6"
---

# Installing stegosip

> TCP tunnel over RTP/SIP.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/stegosip) (version `11.5cda6d6`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S stegosip    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/epinna/Stegosip.git
cd Stegosip
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-dpkt, python2-nfqueue (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[tunnel]] · [[stegosip]] · [[BlackArch Tools]]

