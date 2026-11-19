---
tool: "recomposer"
build-system: "scripts"
upstream: "https://github.com/secretsquirrel/recomposer"
recipe: "blackarch"
source: "https://github.com/secretsquirrel/recomposer"
license: "custom:unknown"
blackarch-pkgver: "2.90f85ed"
---

# Installing recomposer

> Randomly changes Win32/64 PE Files for 'safer' uploading to malware and sandbox sites.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/recomposer) (version `2.90f85ed`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S recomposer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/secretsquirrel/recomposer) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[recomposer]] · [[BlackArch Tools]]

