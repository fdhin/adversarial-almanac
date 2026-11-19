---
tool: "magictree"
build-system: "scripts"
upstream: "http://www.gremwell.com"
recipe: "blackarch"
source: "http://www.gremwell.com/sites/default/files/MagicTree-build1814.jar"
license: "custom:unknown"
blackarch-pkgver: "1.3"
provides: ["magictree"]
---

# Installing magictree

> A penetration tester productivity tool designed to allow easy and straightforward data consolidation, querying, external command execution and report generation

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/magictree) (version `1.3`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S magictree    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.gremwell.com) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `screen`

## Installed layout (per recipe)

- Commands: `/usr/bin/magictree`

Backlinks: [[misc]] · [[magictree]] · [[BlackArch Tools]]

