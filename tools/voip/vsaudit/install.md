---
tool: "vsaudit"
build-system: "scripts"
upstream: "https://github.com/sanvil/vsaudit"
recipe: "blackarch"
source: "https://github.com/sanvil/vsaudit"
license: "MIT"
blackarch-pkgver: "21.2cbc47b"
---

# Installing vsaudit

> VOIP Security Audit Framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vsaudit) (version `21.2cbc47b`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S vsaudit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/sanvil/vsaudit) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`
  - `ruby-pcaprub`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[voip]] · [[vsaudit]] · [[BlackArch Tools]]

