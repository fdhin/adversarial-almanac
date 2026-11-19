---
tool: "hemingway"
build-system: "scripts"
upstream: "https://github.com/ytisf/hemingway"
recipe: "blackarch"
source: "https://github.com/ytisf/hemingway"
license: "GPL3"
blackarch-pkgver: "8.9c70a13"
---

# Installing hemingway

> A simple and easy to use spear phishing helper.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hemingway) (version `8.9c70a13`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S hemingway    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ytisf/hemingway) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[hemingway]] · [[BlackArch Tools]]

