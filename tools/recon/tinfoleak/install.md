---
tool: "tinfoleak"
build-system: "scripts"
upstream: "https://github.com/technoskald/tinfoleak/"
recipe: "blackarch"
source: "https://github.com/technoskald/tinfoleak.git"
license: "custom:unknown"
blackarch-pkgver: "3.6469eb3"
---

# Installing tinfoleak

> Get detailed information about a Twitter user activity.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tinfoleak) (version `3.6469eb3`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S tinfoleak    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/technoskald/tinfoleak.git
cd tinfoleak
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-tweepy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[tinfoleak]] · [[BlackArch Tools]]

