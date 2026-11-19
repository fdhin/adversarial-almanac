---
tool: "ws-attacker"
build-system: "scripts"
upstream: "http://ws-attacker.sourceforge.net/"
recipe: "blackarch"
source: "http://ws-attacker.sourceforge.net/"
license: "custom"
blackarch-pkgver: "1.7"
---

# Installing ws-attacker

> A modular framework for web services penetration testing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ws-attacker) (version `1.7`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S ws-attacker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://ws-attacker.sourceforge.net/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `unzip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[ws-attacker]] · [[BlackArch Tools]]

