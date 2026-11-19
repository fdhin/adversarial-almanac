---
tool: "ipsourcebypass"
build-system: "scripts"
upstream: "https://github.com/p0dalirius/ipsourcebypass"
recipe: "blackarch"
source: "https://github.com/p0dalirius/ipsourcebypass"
license: "custom:unknown"
blackarch-pkgver: "1.2.r17.gd89edb1"
---

# Installing ipsourcebypass

> This Python script can be used to bypass IP source restrictions using HTTP headers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ipsourcebypass) (version `1.2.r17.gd89edb1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ipsourcebypass    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/p0dalirius/ipsourcebypass) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`, `rich`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[ipsourcebypass]] · [[BlackArch Tools]]

