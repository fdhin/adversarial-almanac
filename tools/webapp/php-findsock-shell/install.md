---
tool: "php-findsock-shell"
build-system: "other"
upstream: "https://github.com/pentestmonkey/php-findsock-shell"
recipe: "blackarch"
source: "https://github.com/pentestmonkey/php-findsock-shell"
license: "GPL2"
blackarch-pkgver: "2.b8a984f"
---

# Installing php-findsock-shell

> A Findsock Shell implementation in PHP + C.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/php-findsock-shell) (version `2.b8a984f`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S php-findsock-shell    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/pentestmonkey/php-findsock-shell) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `php`

## Exact build steps from the recipe

```bash
cd $pkgname

  # lame code, lame tool ...
  gcc -o findsock findsock.c
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[php-findsock-shell]] · [[BlackArch Tools]]

