---
tool: "mitmap"
build-system: "scripts"
upstream: "https://github.com/xdavidhu/mitmAP"
recipe: "blackarch"
source: "https://github.com/xdavidhu/mitmAP"
license: "MIT"
blackarch-pkgver: "89.b590f9a"
---

# Installing mitmap

> A python program to create a fake AP and sniff data.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mitmap) (version `89.b590f9a`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S mitmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/xdavidhu/mitmAP.git
cd mitmAP
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `dnspython`, `pcapy`, `twisted`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `sslstrip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[mitmap]] · [[BlackArch Tools]]

