---
tool: "goog-mail"
build-system: "scripts"
upstream: "http://www.darkc0de.com/others/goog-mail.py"
recipe: "blackarch"
source: "http://www.darkc0de.com/others/goog-mail.py"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing goog-mail

> Enumerate domain emails from google.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/goog-mail) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S goog-mail    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.darkc0de.com/others/goog-mail.py) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[goog-mail]] · [[BlackArch Tools]]

