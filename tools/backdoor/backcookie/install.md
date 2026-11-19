---
tool: "backcookie"
build-system: "scripts"
upstream: "https://github.com/mrjopino/backcookie"
recipe: "blackarch"
source: "https://github.com/mrjopino/backcookie"
license: "custom:unknown"
blackarch-pkgver: "51.6dabc38"
---

# Installing backcookie

> Small backdoor using cookie.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/backcookie) (version `51.6dabc38`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S backcookie    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/mrjopino/backcookie) (the recipe uses a versioned archive).

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

Backlinks: [[backdoor]] · [[backcookie]] · [[BlackArch Tools]]

