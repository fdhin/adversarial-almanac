---
tool: "catnthecanary"
build-system: "scripts"
upstream: "https://github.com/packetassailant/catnthecanary"
recipe: "blackarch"
source: "https://github.com/packetassailant/catnthecanary"
license: "MIT"
blackarch-pkgver: "7.e9184fe"
---

# Installing catnthecanary

> An application to query the canary.pw data set for leaked data.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/catnthecanary) (version `7.e9184fe`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S catnthecanary    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/packetassailant/catnthecanary) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-requests, python2-wsgiref (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[catnthecanary]] · [[BlackArch Tools]]

