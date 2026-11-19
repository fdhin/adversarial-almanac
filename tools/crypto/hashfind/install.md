---
tool: "hashfind"
build-system: "scripts"
upstream: "https://github.com/rurapenthe/hashfind"
recipe: "blackarch"
source: "https://github.com/rurapenthe/hashfind"
license: "GPL2"
blackarch-pkgver: "8.e9a9a14"
---

# Installing hashfind

> A tool to search files for matching password hash types and other interesting data.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hashfind) (version `8.e9a9a14`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S hashfind    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/rurapenthe/hashfind) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[hashfind]] · [[BlackArch Tools]]

