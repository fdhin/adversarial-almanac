---
tool: "o365enum"
build-system: "scripts"
upstream: "https://github.com/gremwell/o365enum"
recipe: "blackarch"
source: "https://github.com/gremwell/o365enum"
license: "custom:unknown"
blackarch-pkgver: "19.522a54c"
---

# Installing o365enum

> Username enumeration and password enuming tool aimed at Microsoft O365.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/o365enum) (version `19.522a54c`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S o365enum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/gremwell/o365enum) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[o365enum]] · [[BlackArch Tools]]

