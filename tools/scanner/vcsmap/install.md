---
tool: "vcsmap"
build-system: "scripts"
upstream: "https://github.com/melvinsh/vcsmap"
recipe: "blackarch"
source: "https://github.com/melvinsh/vcsmap"
license: "MIT"
blackarch-pkgver: "v2.0.2.r1.g3889964"
---

# Installing vcsmap

> A plugin-based tool to scan public version control systems for sensitive information.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vcsmap) (version `v2.0.2.r1.g3889964`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S vcsmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/melvinsh/vcsmap) (the recipe uses a versioned archive).

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

Backlinks: [[scanner]] · [[vcsmap]] · [[BlackArch Tools]]

