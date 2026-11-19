---
tool: "nfspy"
build-system: "scripts"
upstream: "https://github.com/bonsaiviking/NfSpy"
recipe: "blackarch"
source: "https://github.com/bonsaiviking/NfSpy"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing nfspy

> A Python library for automating the falsification of NFS credentials when mounting an NFS share.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nfspy) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S nfspy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/bonsaiviking/NfSpy.git
cd NfSpy
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-fuse (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[nfspy]] · [[BlackArch Tools]]

