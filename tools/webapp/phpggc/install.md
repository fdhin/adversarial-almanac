---
tool: "phpggc"
build-system: "scripts"
upstream: "https://github.com/ambionics/phpggc"
recipe: "blackarch"
source: "https://github.com/ambionics/phpggc"
license: "Apache"
blackarch-pkgver: "725.f8aebde"
---

# Installing phpggc

> A library of PHP unserialize() payloads along with a tool to generate them, from command line or programmatically.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/phpggc) (version `725.f8aebde`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S phpggc    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ambionics/phpggc) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `php`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[phpggc]] · [[BlackArch Tools]]

