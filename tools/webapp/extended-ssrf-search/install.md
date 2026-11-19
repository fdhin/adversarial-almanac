---
tool: "extended-ssrf-search"
build-system: "scripts"
upstream: "https://github.com/Damian89/extended-ssrf-search"
recipe: "blackarch"
source: "https://github.com/Damian89/extended-ssrf-search"
license: "custom:unknown"
blackarch-pkgver: "28.680f815"
---

# Installing extended-ssrf-search

> Smart ssrf scanner using different methods like parameter brute forcing in post and get.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/extended-ssrf-search) (version `28.680f815`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S extended-ssrf-search    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Damian89/extended-ssrf-search) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[extended-ssrf-search]] · [[BlackArch Tools]]

