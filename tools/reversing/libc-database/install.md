---
tool: "libc-database"
build-system: "scripts"
upstream: "https://github.com/niklasb/libc-database"
recipe: "blackarch"
source: "https://github.com/niklasb/libc-database"
license: "custom:unknown"
blackarch-pkgver: "45.69815cd"
provides: ["libc-"]
---

# Installing libc-database

> Database of libc offsets to simplify exploitation.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/libc-database) (version `45.69815cd`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S libc-database    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/niklasb/libc-database) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `wget`

## Installed layout (per recipe)

- Commands: `/usr/bin/libc-`

Backlinks: [[reversing]] · [[libc-database]] · [[BlackArch Tools]]

