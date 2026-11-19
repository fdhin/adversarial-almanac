---
tool: "pentbox"
build-system: "scripts"
upstream: "http://www.pentbox.net"
recipe: "blackarch"
aur-package: "pentbox"
source: "http://www.pentbox.net"
license: "GPL3"
blackarch-pkgver: "1.8"
---

# Installing pentbox

> A security suite that packs security and stability testing oriented tools for networks and systems.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pentbox) (version `1.8`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pentbox    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.pentbox.net) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `ruby`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[pentbox]] · [[BlackArch Tools]]

