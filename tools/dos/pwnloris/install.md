---
tool: "pwnloris"
build-system: "scripts"
upstream: "https://github.com/h0ussni/pwnloris"
recipe: "blackarch"
source: "https://github.com/h0ussni/pwnloris"
license: "GPL-3.0-or-later"
blackarch-pkgver: "14.7ad1486"
---

# Installing pwnloris

> An improved slowloris DOS tool which keeps attacking until the server starts getting exhausted.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pwnloris) (version `14.7ad1486`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S pwnloris    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/h0ussni/pwnloris) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pysocks`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[dos]] · [[pwnloris]] · [[BlackArch Tools]]

