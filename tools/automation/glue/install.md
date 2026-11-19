---
tool: "glue"
build-system: "scripts"
upstream: "https://github.com/OWASP/glue"
recipe: "blackarch"
source: "https://github.com/OWASP/glue"
license: "APACHE"
blackarch-pkgver: "380.8703380"
---

# Installing glue

> A framework for running a series of tools.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/glue) (version `380.8703380`, license APACHE).

## Arch / BlackArch

```bash
sudo pacman -S glue    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/OWASP/glue) (the recipe uses a versioned archive).

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

Backlinks: [[automation]] · [[glue]] · [[BlackArch Tools]]

