---
tool: "vfeed"
build-system: "scripts"
upstream: "http://www.toolswatch.org/vfeed"
recipe: "blackarch"
source: "http://www.toolswatch.org/vfeed"
license: "BSD"
blackarch-pkgver: "81.fad17ae"
---

# Installing vfeed

> Open Source Cross Linked and Aggregated Local Vulnerability Database main repository.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vfeed) (version `81.fad17ae`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S vfeed    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/toolswatch/vFeed.git
cd vFeed
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[vfeed]] · [[BlackArch Tools]]

