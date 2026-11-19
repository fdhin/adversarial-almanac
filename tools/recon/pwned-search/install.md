---
tool: "pwned-search"
build-system: "scripts"
upstream: "https://github.com/mikepound/pwned-search"
recipe: "blackarch"
source: "https://github.com/mikepound/pwned-search"
license: "Unlicense"
blackarch-pkgver: "40.04c1439"
---

# Installing pwned-search

> Pwned Password API lookup.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pwned-search) (version `40.04c1439`, license Unlicense).

## Arch / BlackArch

```bash
sudo pacman -S pwned-search    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/mikepound/pwned-search) (the recipe uses a versioned archive).

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

Backlinks: [[recon]] · [[pwned-search]] · [[BlackArch Tools]]

