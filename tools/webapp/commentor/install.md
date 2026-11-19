---
tool: "commentor"
build-system: "scripts"
upstream: "https://github.com/D3vil0p3r/comMENTOR"
recipe: "blackarch"
source: "https://github.com/D3vil0p3r/comMENTOR"
license: "GPL3"
blackarch-pkgver: "20.4582674"
---

# Installing commentor

> Extract all comments from the specified URL resource.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/commentor) (version `20.4582674`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S commentor    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/D3vil0p3r/comMENTOR) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `curl`
  - `tidy`
  - `xmlstarlet`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[commentor]] · [[BlackArch Tools]]

