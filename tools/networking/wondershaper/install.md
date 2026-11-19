---
tool: "wondershaper"
build-system: "scripts"
upstream: "https://github.com/magnific0/wondershaper"
recipe: "blackarch"
source: "https://github.com/magnific0/wondershaper"
license: "GPL2"
blackarch-pkgver: "48.98792b5"
provides: ["wondershaper"]
---

# Installing wondershaper

> Limit the bandwidth of one or more network adapters.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wondershaper) (version `48.98792b5`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S wondershaper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/magnific0/wondershaper) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `iproute`
  - `kmod`

## Installed layout (per recipe)

- Commands: `/usr/bin/wondershaper`

Backlinks: [[networking]] · [[wondershaper]] · [[BlackArch Tools]]

