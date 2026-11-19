---
tool: "webhandler"
build-system: "scripts"
upstream: "https://github.com/lnxg33k/webhandler"
recipe: "blackarch"
source: "https://github.com/lnxg33k/webhandler.git"
license: "custom:unknown"
blackarch-pkgver: "348.1bd971e"
---

# Installing webhandler

> A handler for PHP system functions & also an alternative 'netcat' handler.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/webhandler) (version `348.1bd971e`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S webhandler    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/lnxg33k/webhandler.git
cd webhandler
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[webhandler]] · [[BlackArch Tools]]

