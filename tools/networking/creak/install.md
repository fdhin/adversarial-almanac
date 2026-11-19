---
tool: "creak"
build-system: "scripts"
upstream: "https://github.com/codepr/creak"
recipe: "blackarch"
source: "https://github.com/codepr/creak"
license: "GPL3"
blackarch-pkgver: "41.a6c011f"
---

# Installing creak

> Poison, reset, spoof, redirect MITM script.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/creak) (version `41.a6c011f`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S creak    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/codepr/creak) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-dpkt, python2-pydnet, python2-pypcap, python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[creak]] · [[BlackArch Tools]]

