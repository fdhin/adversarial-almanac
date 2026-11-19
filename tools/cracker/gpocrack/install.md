---
tool: "gpocrack"
build-system: "scripts"
upstream: "https://github.com/MartinIngesen/gpocrack"
recipe: "blackarch"
source: "https://github.com/MartinIngesen/gpocrack"
license: "custom:unknown"
blackarch-pkgver: "3.cf63c86"
---

# Installing gpocrack

> Active Directory Group Policy Preferences cpassword cracker/decrypter.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gpocrack) (version `3.cf63c86`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S gpocrack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/MartinIngesen/gpocrack) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pycryptodome`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[gpocrack]] · [[BlackArch Tools]]

