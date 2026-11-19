---
tool: "lans"
build-system: "scripts"
upstream: "https://github.com/DanMcInerney/LANs.py"
recipe: "blackarch"
source: "https://github.com/DanMcInerney/LANs.py"
license: "GPL"
blackarch-pkgver: "148.9f8ef2d"
---

# Installing lans

> A Multithreaded asynchronous packet parsing/injecting arp spoofer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lans) (version `148.9f8ef2d`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S lans    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/DanMcInerney/LANs.py.git
cd LANs.py
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `aircrack-ng`
  - `nbtscan`
  - `python2`
- ⚠️ Python 2 only deps: python2-nfqueue, python2-requests, python2-scapy, python2-twistedx (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[spoof]] · [[lans]] · [[BlackArch Tools]]

