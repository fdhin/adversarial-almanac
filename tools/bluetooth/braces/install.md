---
tool: "braces"
build-system: "perl"
upstream: "http://braces.shmoo.com/"
recipe: "blackarch"
aur-package: "braces"
source: "http://braces.shmoo.com/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "0.5"
---

# Installing braces

> A Bluetooth Tracking Utility.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/braces) (version `0.5`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S braces    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://braces.shmoo.com/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bluez`
  - `imagemagick` — Debian imagemagick / Fedora ImageMagick
  - `mariadb`
  - `nginx`
  - `perl`
  - `perl-net-dbus`
  - `perl-uri`
  - `php-fpm`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[bluetooth]] · [[braces]] · [[BlackArch Tools]]

