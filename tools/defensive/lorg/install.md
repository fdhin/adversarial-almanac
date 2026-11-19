---
tool: "lorg"
build-system: "scripts"
upstream: "https://github.com/jensvoid/lorg"
recipe: "blackarch"
source: "https://github.com/jensvoid/lorg"
license: "GPL2"
blackarch-pkgver: "98.aa4f1a3"
---

# Installing lorg

> Apache Logfile Security Analyzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lorg) (version `98.aa4f1a3`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S lorg    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/jensvoid/lorg) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `php`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[lorg]] · [[BlackArch Tools]]

