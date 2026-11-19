---
tool: "sqid"
build-system: "scripts"
upstream: "http://sqid.rubyforge.org/"
recipe: "blackarch"
source: "http://sqid.rubyforge.org/"
license: "GPL"
blackarch-pkgver: "0.3"
---

# Installing sqid

> A SQL injection digger.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sqid) (version `0.3`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S sqid    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://sqid.rubyforge.org/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `ruby`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[sqid]] · [[BlackArch Tools]]

