---
tool: "matahari"
build-system: "scripts"
upstream: "http://matahari.sourceforge.net/"
recipe: "blackarch"
source: "http://matahari.sourceforge.net/"
license: "GPL"
blackarch-pkgver: "0.1.30"
---

# Installing matahari

> A reverse HTTP shell to execute commands on remote machines behind firewalls.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/matahari) (version `0.1.30`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S matahari    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://matahari.sourceforge.net/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[tunnel]] · [[matahari]] · [[BlackArch Tools]]

