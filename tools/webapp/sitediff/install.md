---
tool: "sitediff"
build-system: "scripts"
upstream: "https://github.com/digininja/sitediff"
recipe: "blackarch"
source: "https://github.com/digininja/sitediff"
license: "custom:unknown"
blackarch-pkgver: "3.1383935"
---

# Installing sitediff

> Fingerprint a web app using local files as the fingerprint sources.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sitediff) (version `3.1383935`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S sitediff    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/digininja/sitediff) (the recipe uses a versioned archive).

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

Backlinks: [[webapp]] · [[sitediff]] · [[BlackArch Tools]]

