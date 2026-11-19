---
tool: "osintgram"
build-system: "scripts"
upstream: "https://github.com/Datalux/Osintgram"
recipe: "blackarch"
aur-package: "osintgram"
source: "https://github.com/Datalux/Osintgram"
license: "GPL3"
blackarch-pkgver: "2.0.r11.ga560498"
---

# Installing osintgram

> OSINT tool offering an interactive shell to perform analysis on Instagram account of any users by its nickname.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/osintgram) (version `2.0.r11.ga560498`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S osintgram    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Datalux/Osintgram) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `geopy`, `gnureadline`, `instagram-private-api`, `prettytable`, `requests`, `requests-toolbelt`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[osintgram]] · [[BlackArch Tools]]

