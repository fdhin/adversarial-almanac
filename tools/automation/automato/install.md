---
tool: "automato"
build-system: "scripts"
upstream: "https://github.com/skahwah/automato"
recipe: "blackarch"
source: "https://github.com/skahwah/automato"
license: "custom:unknown"
blackarch-pkgver: "33.0561b59"
---

# Installing automato

> Should help with automating some of the user-focused enumeration tasks during an internal penetration test.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/automato) (version `33.0561b59`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S automato    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/skahwah/automato) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`
  - `ruby-bundler`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[automato]] · [[BlackArch Tools]]

