---
tool: "gitmails"
build-system: "scripts"
upstream: "https://github.com/giovanifss/gitmails"
recipe: "blackarch"
source: "https://github.com/giovanifss/gitmails"
license: "MIT"
blackarch-pkgver: "0.1.1.r6.g8aa8411"
---

# Installing gitmails

> An information gathering tool to collect git commit emails in version control host services.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gitmails) (version `0.1.1.r6.g8aa8411`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S gitmails    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/giovanifss/gitmails) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pygit2`, `requests`, `tabulate`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[gitmails]] · [[BlackArch Tools]]

