---
tool: "webspa"
build-system: "scripts"
upstream: "http://sourceforge.net/projects/webspa/"
recipe: "blackarch"
source: "http://sourceforge.net/projects/webspa/"
license: "GPL3"
blackarch-pkgver: "0.8"
---

# Installing webspa

> A web knocking tool, sending a single HTTP/S to run O/S commands.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/webspa) (version `0.8`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S webspa    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/webspa/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `bash`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[webspa]] · [[BlackArch Tools]]

