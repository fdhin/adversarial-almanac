---
tool: "pyjfuzz"
build-system: "scripts"
upstream: "https://github.com/mseclab/PyJFuzz"
recipe: "blackarch"
source: "https://github.com/mseclab/PyJFuzz"
license: "MIT"
blackarch-pkgver: "159.578d44c"
provides: ["pjf"]
---

# Installing pyjfuzz

> Python JSON Fuzzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pyjfuzz) (version `159.578d44c`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pyjfuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/mseclab/PyJFuzz.git
cd PyJFuzz
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-bottle, python2-gitpython, python2-gramfuzz, python2-netifaces, python2-pyopenssl, python2-setuptools, python2-wsgiref (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/pjf`

Backlinks: [[fuzzer]] · [[pyjfuzz]] · [[BlackArch Tools]]

