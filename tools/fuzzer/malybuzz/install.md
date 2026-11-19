---
tool: "malybuzz"
build-system: "scripts"
upstream: "http://eternal-todo.com/tools/malybuzz-network-fuzzer"
recipe: "blackarch"
source: "http://eternal-todo.com/tools/malybuzz-network-fuzzer"
license: "BSD"
blackarch-pkgver: "1.0"
---

# Installing malybuzz

> A Python tool focused in discovering programming faults in network software.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/malybuzz) (version `1.0`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S malybuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://eternal-todo.com/tools/malybuzz-network-fuzzer) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-lxml (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[malybuzz]] · [[BlackArch Tools]]

