---
tool: "ysoserial"
build-system: "scripts"
upstream: "https://github.com/frohoff/ysoserial"
recipe: "blackarch"
source: "https://github.com/frohoff/ysoserial"
license: "MIT"
blackarch-pkgver: "0.0.6"
---

# Installing ysoserial

> A proof-of-concept tool for generating payloads that exploit unsafe Java object deserialization.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ysoserial) (version `0.0.6`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S ysoserial    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/frohoff/ysoserial) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[ysoserial]] · [[BlackArch Tools]]

