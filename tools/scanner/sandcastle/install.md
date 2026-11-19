---
tool: "sandcastle"
build-system: "scripts"
upstream: "https://github.com/0xSearches/sandcastle"
recipe: "blackarch"
source: "https://github.com/0xSearches/sandcastle"
license: "MIT"
blackarch-pkgver: "73.10af7c7"
---

# Installing sandcastle

> A Python script for AWS S3 bucket enumeration.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sandcastle) (version `73.10af7c7`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S sandcastle    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/0xSearches/sandcastle) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[sandcastle]] · [[BlackArch Tools]]

