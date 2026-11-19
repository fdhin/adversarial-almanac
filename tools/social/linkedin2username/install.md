---
tool: "linkedin2username"
build-system: "scripts"
upstream: "https://github.com/initstring/linkedin2username"
recipe: "blackarch"
source: "https://github.com/initstring/linkedin2username"
license: "MIT"
blackarch-pkgver: "0.30.r0.g317f35f"
---

# Installing linkedin2username

> OSINT Tool: Generate username lists for companies on LinkedIn.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/linkedin2username) (version `0.30.r0.g317f35f`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S linkedin2username    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/initstring/linkedin2username) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[linkedin2username]] · [[BlackArch Tools]]

