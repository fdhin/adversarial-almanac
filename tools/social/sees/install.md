---
tool: "sees"
build-system: "scripts"
upstream: "https://github.com/galkan/sees/"
recipe: "blackarch"
source: "https://github.com/galkan/sees/"
license: "custom"
blackarch-pkgver: "67.cd741aa"
---

# Installing sees

> Increase the success rate of phishing attacks by sending emails to company users as if they are coming from the very same company's domain.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sees) (version `67.cd741aa`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S sees    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/galkan/sees/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `postfix`
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[sees]] · [[BlackArch Tools]]

