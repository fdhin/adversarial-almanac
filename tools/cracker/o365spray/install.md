---
tool: "o365spray"
build-system: "scripts"
upstream: "https://github.com/0xZDH/o365spray"
recipe: "blackarch"
source: "https://github.com/0xZDH/o365spray"
license: "custom:unknown"
blackarch-pkgver: "160.28d8d1b"
---

# Installing o365spray

> Username enumeration and password spraying tool aimed at Microsoft O365.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/o365spray) (version `160.28d8d1b`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S o365spray    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/0xZDH/o365spray) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[o365spray]] · [[BlackArch Tools]]

