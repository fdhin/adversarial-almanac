---
tool: "token-reverser"
build-system: "scripts"
upstream: "https://github.com/dariusztytko/token-reverser"
recipe: "blackarch"
source: "https://github.com/dariusztytko/token-reverser"
license: "GPL3"
blackarch-pkgver: "10.cbb4528"
---

# Installing token-reverser

> Word list generator to crack security tokens.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/token-reverser) (version `10.cbb4528`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S token-reverser    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/dariusztytko/token-reverser) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[token-reverser]] · [[BlackArch Tools]]

