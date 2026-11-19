---
tool: "nomorexor"
build-system: "scripts"
upstream: "https://github.com/hiddenillusion/NoMoreXOR"
recipe: "blackarch"
source: "https://github.com/hiddenillusion/NoMoreXOR"
license: "custom:unknown"
blackarch-pkgver: "2.84489f9"
---

# Installing nomorexor

> Tool to help guess a files 256 byte XOR key by using frequency analysis.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nomorexor) (version `2.84489f9`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S nomorexor    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/hiddenillusion/NoMoreXOR.git
cd NoMoreXOR
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-yara (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[nomorexor]] · [[BlackArch Tools]]

