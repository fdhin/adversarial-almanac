---
tool: "lyricpass"
build-system: "scripts"
upstream: "https://github.com/initstring/lyricpass"
recipe: "blackarch"
source: "https://github.com/initstring/lyricpass"
license: "GPL3"
blackarch-pkgver: "45.fd31d07"
---

# Installing lyricpass

> Tool to generate wordlists based on lyrics.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lyricpass) (version `45.fd31d07`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S lyricpass    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/initstring/lyricpass) (the recipe uses a versioned archive).

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

Backlinks: [[automation]] · [[lyricpass]] · [[BlackArch Tools]]

