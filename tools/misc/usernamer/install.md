---
tool: "usernamer"
build-system: "scripts"
upstream: "https://github.com/jseidl/usernamer"
recipe: "blackarch"
source: "https://github.com/jseidl/usernamer"
license: "GPL3"
blackarch-pkgver: "20.12983f8"
---

# Installing usernamer

> Pentest Tool to generate usernames/logins based on supplied names.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/usernamer) (version `20.12983f8`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S usernamer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/jseidl/usernamer) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[usernamer]] · [[BlackArch Tools]]

