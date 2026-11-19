---
tool: "kitty-framework"
build-system: "scripts"
upstream: "https://github.com/cisco-sas/kitty"
recipe: "blackarch"
source: "https://github.com/cisco-sas/kitty"
license: "GPL2"
blackarch-pkgver: "352.cb07609"
---

# Installing kitty-framework

> Fuzzing framework written in python.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kitty-framework) (version `352.cb07609`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S kitty-framework    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/cisco-sas/kitty.git
cd kitty
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-bitstring, python2-docopt, python2-requests, python2-setuptools, python2-six (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[kitty-framework]] · [[BlackArch Tools]]

