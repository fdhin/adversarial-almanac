---
tool: "xorbruteforcer"
build-system: "scripts"
upstream: "http://eternal-todo.com/category/bruteforce"
recipe: "blackarch"
source: "http://eternal-todo.com/files/scripts/xorBruteForcer.py"
license: "unknown"
blackarch-pkgver: "0.1"
---

# Installing xorbruteforcer

> Script that implements a XOR bruteforcing of a given file, although a specific key can be used too.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xorbruteforcer) (version `0.1`, license n/a).

## Arch / BlackArch

```bash
sudo pacman -S xorbruteforcer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://eternal-todo.com/category/bruteforce) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[xorbruteforcer]] · [[BlackArch Tools]]

