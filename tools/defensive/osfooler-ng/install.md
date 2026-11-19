---
tool: "osfooler-ng"
build-system: "scripts"
upstream: "https://github.com/segofensiva/OSfooler-ng"
recipe: "blackarch"
source: "https://github.com/segofensiva/OSfooler-ng"
license: "GPL3"
blackarch-pkgver: "2.c0b20d6"
---

# Installing osfooler-ng

> Prevents remote active/passive OS fingerprinting by tools like nmap or p0f.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/osfooler-ng) (version `2.c0b20d6`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S osfooler-ng    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/segofensiva/OSfooler-ng.git
cd OSfooler-ng
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-dpkt, python2-nfqueue, python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[osfooler-ng]] · [[BlackArch Tools]]

