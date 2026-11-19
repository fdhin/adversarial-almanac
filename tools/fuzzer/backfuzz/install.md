---
tool: "backfuzz"
build-system: "scripts"
upstream: "https://github.com/localh0t/backfuzz"
recipe: "blackarch"
source: "https://github.com/localh0t/backfuzz"
license: "GPL3"
blackarch-pkgver: "1.b0648de"
---

# Installing backfuzz

> A network protocol fuzzing toolkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/backfuzz) (version `1.b0648de`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S backfuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/localh0t/backfuzz) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[backfuzz]] · [[BlackArch Tools]]

