---
tool: "uptux"
build-system: "scripts"
upstream: "https://github.com/initstring/uptux"
recipe: "blackarch"
source: "https://github.com/initstring/uptux"
license: "MIT"
blackarch-pkgver: "33.85ccfd0"
---

# Installing uptux

> Linux privilege escalation checks (systemd, dbus, socket fun, etc).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/uptux) (version `33.85ccfd0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S uptux    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/initstring/uptux) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[uptux]] · [[BlackArch Tools]]

