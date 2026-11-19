---
tool: "nexfil"
build-system: "scripts"
upstream: "https://github.com/thewhiteh4t/nexfil"
recipe: "blackarch"
aur-package: "nexfil"
source: "https://github.com/thewhiteh4t/nexfil"
license: "MIT"
blackarch-pkgver: "54.c540238"
---

# Installing nexfil

> OSINT tool for finding profiles by username.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nexfil) (version `54.c540238`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S nexfil    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/thewhiteh4t/nexfil) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `aiodns`, `aiohttp`, `cchardet`, `requests`, `tldextract`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[nexfil]] · [[BlackArch Tools]]

