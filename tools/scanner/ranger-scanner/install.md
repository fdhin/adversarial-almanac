---
tool: "ranger-scanner"
build-system: "scripts"
upstream: "https://github.com/funkandwagnalls/ranger"
recipe: "blackarch"
source: "https://github.com/funkandwagnalls/ranger"
license: "custom:unknown"
blackarch-pkgver: "149.3aae5dd"
---

# Installing ranger-scanner

> A tool to support security professionals to access and interact with remote Microsoft Windows based systems.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ranger-scanner) (version `149.3aae5dd`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ranger-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/funkandwagnalls/ranger.git
cd ranger
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-colorama, python2-impacket, python2-lxml, python2-netaddr, python2-netifaces, python2-pycryptodome, python2-python-nmap, python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[ranger-scanner]] · [[BlackArch Tools]]

