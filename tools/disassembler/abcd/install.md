---
tool: "abcd"
build-system: "scripts"
upstream: "https://github.com/MITRECND/abcd"
recipe: "blackarch"
source: "https://github.com/MITRECND/abcd"
license: "BSD-2-Clause"
blackarch-pkgver: "4.2738809"
provides: ["abcdump", "abcgraph"]
---

# Installing abcd

> ActionScript ByteCode Disassembler.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/abcd) (version `4.2738809`, license BSD-2-Clause).

## Arch / BlackArch

```bash
sudo pacman -S abcd    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/MITRECND/abcd) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pyswf, python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/abcdump`, `/usr/bin/abcgraph`

Backlinks: [[disassembler]] · [[abcd]] · [[BlackArch Tools]]

