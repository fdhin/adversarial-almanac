---
tool: "takeover"
build-system: "scripts"
upstream: "https://github.com/m4ll0k/takeover"
recipe: "blackarch"
source: "https://github.com/m4ll0k/takeover"
license: "MIT"
blackarch-pkgver: "98.a058647"
---

# Installing takeover

> Sub-Domain TakeOver Vulnerability Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/takeover) (version `98.a058647`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S takeover    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/m4ll0k/takeover) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[takeover]] · [[BlackArch Tools]]

