---
tool: "snuck"
build-system: "scripts"
upstream: "https://github.com/mauro-g/snuck"
recipe: "blackarch"
source: "https://github.com/mauro-g/snuck"
license: "custom:unknown"
blackarch-pkgver: "6.76196b6"
---

# Installing snuck

> Automatic XSS filter bypass.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/snuck) (version `6.76196b6`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S snuck    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/mauro-g/snuck) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `apache-ant`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[snuck]] · [[BlackArch Tools]]

