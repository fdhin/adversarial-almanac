---
tool: "peepingtom"
build-system: "scripts"
upstream: "https://bitbucket.org/LaNMaSteR53/peepingtom"
recipe: "blackarch"
source: "https://bitbucket.org/LaNMaSteR53/peepingtom"
license: "GPL3"
blackarch-pkgver: "56.bc6f4d8"
---

# Installing peepingtom

> A tool to take screenshots of websites. Much like eyewitness.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/peepingtom) (version `56.bc6f4d8`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S peepingtom    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://bitbucket.org/LaNMaSteR53/peepingtom) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `curl`
  - `phantomjs`
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[peepingtom]] · [[BlackArch Tools]]

