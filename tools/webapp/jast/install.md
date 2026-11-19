---
tool: "jast"
build-system: "scripts"
upstream: "https://github.com/mikehacksthings/jast"
recipe: "blackarch"
source: "https://github.com/mikehacksthings/jast"
license: "custom::unknown"
blackarch-pkgver: "17.361ecde"
---

# Installing jast

> Just Another Screenshot Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jast) (version `17.361ecde`, license custom::unknown).

## Arch / BlackArch

```bash
sudo pacman -S jast    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/mikehacksthings/jast) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `docopt`, `requests`, `selenium`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[jast]] · [[BlackArch Tools]]

