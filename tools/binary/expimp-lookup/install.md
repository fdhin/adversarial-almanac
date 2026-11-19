---
tool: "expimp-lookup"
build-system: "scripts"
upstream: "https://github.com/tr3w/ExpImp-Lookup"
recipe: "blackarch"
source: "https://github.com/tr3w/PEace.git"
license: "GPL2"
blackarch-pkgver: "4.79a96c7"
---

# Installing expimp-lookup

> Looks for all export and import names that contain a specified string in all Portable Executable in a directory tree.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/expimp-lookup) (version `4.79a96c7`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S expimp-lookup    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/tr3w/ExpImp-Lookup.git
cd ExpImp-Lookup
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[expimp-lookup]] · [[BlackArch Tools]]

