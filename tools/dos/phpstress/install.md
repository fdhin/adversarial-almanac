---
tool: "phpstress"
build-system: "scripts"
upstream: "https://github.com/nightlionsecurity/phpstress"
recipe: "blackarch"
source: "https://github.com/nightlionsecurity/phpstress"
license: "MIT"
blackarch-pkgver: "5.f987a7e"
---

# Installing phpstress

> A PHP denial of service / stress test for Web Servers running PHP-FPM or PHP-CGI.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/phpstress) (version `5.f987a7e`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S phpstress    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/nightlionsecurity/phpstress) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `php`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[dos]] · [[phpstress]] · [[BlackArch Tools]]

