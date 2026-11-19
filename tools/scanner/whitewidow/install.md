---
tool: "whitewidow"
build-system: "scripts"
upstream: "https://github.com/Ekultek/whitewidow"
recipe: "blackarch"
source: "https://github.com/Ekultek/whitewidow"
license: "GPL3, custom:unknown"
blackarch-pkgver: "605.4f27bfe"
---

# Installing whitewidow

> SQL Vulnerability Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/whitewidow) (version `605.4f27bfe`, license GPL3, custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S whitewidow    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Ekultek/whitewidow) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `ruby`
  - `ruby-bundler`
  - `ruby-pkg-config`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[whitewidow]] · [[BlackArch Tools]]

