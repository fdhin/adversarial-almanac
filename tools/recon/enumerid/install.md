---
tool: "enumerid"
build-system: "scripts"
upstream: "https://github.com/Gilks/enumerid"
recipe: "blackarch"
source: "https://github.com/Gilks/enumerid"
license: "BSD"
blackarch-pkgver: "37.ba5540f"
---

# Installing enumerid

> Enumerate RIDs using pure Python.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/enumerid) (version `37.ba5540f`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S enumerid    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Gilks/enumerid) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-dnspython, python2-impacket (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[enumerid]] · [[BlackArch Tools]]

