---
tool: "whatsmyname"
build-system: "scripts"
upstream: "https://github.com/WebBreacher/WhatsMyName"
recipe: "blackarch"
source: "https://github.com/WebBreacher/WhatsMyName"
license: "MIT"
blackarch-pkgver: "2934.0021b59"
---

# Installing whatsmyname

> Tool to perform user and username enumeration on various websites.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/whatsmyname) (version `2934.0021b59`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S whatsmyname    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/WebBreacher/WhatsMyName.git
cd WhatsMyName
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `aiohttp`, `multidict`, `requests`, `terminaltables`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[whatsmyname]] · [[BlackArch Tools]]

