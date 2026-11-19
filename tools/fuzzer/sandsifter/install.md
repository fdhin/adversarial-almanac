---
tool: "sandsifter"
build-system: "scripts"
upstream: "https://github.com/xoreaxeaxeax/sandsifter"
recipe: "blackarch"
source: "https://github.com/xoreaxeaxeax/sandsifter"
license: "custom:unknown"
blackarch-pkgver: "2.8375e61"
provides: ["sifter", "summarize"]
---

# Installing sandsifter

> The x86 processor fuzzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sandsifter) (version `2.8375e61`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S sandsifter    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/xoreaxeaxeax/sandsifter) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `make`
- **System libraries** (Arch name — translate for your distro):
  - `capstone`
  - `python2`
  - `sed`
- ⚠️ Python 2 only deps: python2-argparse (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/sifter`, `/usr/bin/summarize`

Backlinks: [[fuzzer]] · [[sandsifter]] · [[BlackArch Tools]]

