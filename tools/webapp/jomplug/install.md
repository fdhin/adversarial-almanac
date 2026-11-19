---
tool: "jomplug"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/121390/Janissaries-Joomla-Fingerprint-Tool.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/121390/Janissaries-Joomla-Fingerprint-Tool.html"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing jomplug

> This php script fingerprints a given Joomla system and then uses Packet Storm's archive to check for bugs related to the installed components.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jomplug) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S jomplug    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/121390/Janissaries-Joomla-Fingerprint-Tool.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `php`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[jomplug]] · [[BlackArch Tools]]

