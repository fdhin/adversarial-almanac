---
tool: "owabf"
build-system: "scripts"
upstream: "http://netsec.rs/70/tools.html"
recipe: "blackarch"
source: "http://netsec.rs/70/tools.html"
license: "custom:unknown"
blackarch-pkgver: "1.3"
---

# Installing owabf

> Outlook Web Access bruteforcer tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/owabf) (version `1.3`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S owabf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://netsec.rs/70/tools.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[owabf]] · [[BlackArch Tools]]

