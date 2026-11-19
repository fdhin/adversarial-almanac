---
tool: "hodor"
build-system: "scripts"
upstream: "https://github.com/nccgroup/hodor"
recipe: "blackarch"
source: "https://github.com/nccgroup/hodor"
license: "custom:unknown"
blackarch-pkgver: "1.01be107"
---

# Installing hodor

> A general-use fuzzer that can be configured to use known-good input and delimiters in order to fuzz specific locations.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hodor) (version `1.01be107`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S hodor    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/nccgroup/hodor) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[hodor]] · [[BlackArch Tools]]

