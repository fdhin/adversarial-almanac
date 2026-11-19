---
tool: "flask-session-cookie-manager3"
build-system: "scripts"
upstream: "https://noraj.github.io/flask-session-cookie-manager/"
recipe: "split-parent:flask-session-cookie-manager"
source: "https://noraj.github.io/flask-session-cookie-manager/"
license: "MIT"
blackarch-pkgver: "v1.2.2.r1.g3c271d2"
---

# Installing flask-session-cookie-manager3

> Decode and encode Flask session cookie.

ℹ️ Packaged as a split package — built from the `flask-session-cookie-manager` recipe.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/flask-session-cookie-manager) (version `v1.2.2.r1.g3c271d2`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S flask-session-cookie-manager3    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://noraj.github.io/flask-session-cookie-manager/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[flask-session-cookie-manager3]] · [[BlackArch Tools]]

