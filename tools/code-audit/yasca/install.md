---
tool: "yasca"
build-system: "scripts"
upstream: "https://sourceforge.net/projects/yasca/files/Yasca%202.x/"
recipe: "blackarch"
source: "https://sourceforge.net/projects/yasca/files/Yasca%202.x/"
license: "BSD"
blackarch-pkgver: "2.1"
---

# Installing yasca

> Multi-Language Static Analysis Toolset.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/yasca) (version `2.1`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S yasca    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.scovetta.com/yasca.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `php`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[yasca]] · [[BlackArch Tools]]

