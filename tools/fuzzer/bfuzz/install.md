---
tool: "bfuzz"
build-system: "scripts"
upstream: "https://github.com/RootUp/BFuzz"
recipe: "blackarch"
source: "https://github.com/RootUp/BFuzz"
license: "custom:unknown"
blackarch-pkgver: "60.fdaefc0"
---

# Installing bfuzz

> Input based fuzzer tool for browsers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bfuzz) (version `60.fdaefc0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S bfuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/RootUp/BFuzz) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[bfuzz]] · [[BlackArch Tools]]

