---
tool: "crackq"
build-system: "scripts"
upstream: "https://github.com/vnik5287/Crackq"
recipe: "blackarch"
source: "https://github.com/vnik5287/Crackq"
license: "custom:unknown"
blackarch-pkgver: "48.89b7318"
provides: ["crackqcli"]
---

# Installing crackq

> Hashcrack.org GPU-accelerated password cracker.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/crackq) (version `48.89b7318`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S crackq    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/vnik5287/Crackq.git
cd Crackq
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-simplejson (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/crackqcli`

Backlinks: [[cracker]] · [[crackq]] · [[BlackArch Tools]]

