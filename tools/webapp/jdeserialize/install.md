---
tool: "jdeserialize"
build-system: "scripts"
upstream: "https://github.com/frohoff/jdeserialize/"
recipe: "blackarch"
aur-package: "jdeserialize"
source: "https://github.com/frohoff/jdeserialize/"
license: "custom:unknown"
blackarch-pkgver: "31.20635ba"
---

# Installing jdeserialize

> A library that interprets Java serialized objects. It also comes with a command-line tool that can generate compilable class declarations, extract block data, and print textual representations of instance values.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jdeserialize) (version `31.20635ba`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S jdeserialize    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/frohoff/jdeserialize/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `ant`
  - `java-runtime`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[jdeserialize]] · [[BlackArch Tools]]

