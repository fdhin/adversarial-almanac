---
tool: "joomlavs"
build-system: "scripts"
upstream: "https://github.com/rastating/joomlavs"
recipe: "blackarch"
source: "https://github.com/rastating/joomlavs"
license: "GPL3"
blackarch-pkgver: "254.eea7500"
---

# Installing joomlavs

> A black box, Ruby powered, Joomla vulnerability scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/joomlavs) (version `254.eea7500`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S joomlavs    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/rastating/joomlavs) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `ruby`
  - `ruby-bundler`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[joomlavs]] · [[BlackArch Tools]]

