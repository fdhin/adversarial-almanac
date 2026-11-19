---
tool: "cupp"
build-system: "scripts"
upstream: "http://www.remote-exploit.org/?page_id=418"
recipe: "blackarch"
source: "http://www.remote-exploit.org/?page_id=418"
license: "GPL-3.0-or-later"
blackarch-pkgver: "87.b77f1ae"
---

# Installing cupp

> Common User Password Profiler

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cupp) (version `87.b77f1ae`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S cupp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.remote-exploit.org/?page_id=418) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[cupp]] · [[BlackArch Tools]]

