---
tool: "eapeak"
build-system: "scripts"
upstream: "https://github.com/securestate/eapeak"
recipe: "blackarch"
source: "https://github.com/securestate/eapeak"
license: "GPL2, GPL3"
blackarch-pkgver: "130.9550d1c"
---

# Installing eapeak

> Analysis Suite For EAP Enabled Wireless Networks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/eapeak) (version `130.9550d1c`, license GPL2, GPL3).

## Arch / BlackArch

```bash
sudo pacman -S eapeak    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/securestate/eapeak) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-m2crypto, python2-scapy, python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[eapeak]] · [[BlackArch Tools]]

