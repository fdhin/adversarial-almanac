---
tool: "wireless-ids"
build-system: "scripts"
upstream: "https://github.com/SYWorks/wireless-ids"
recipe: "blackarch"
source: "https://github.com/SYWorks/wireless-ids"
license: "GPL2"
blackarch-pkgver: "24.b132071"
---

# Installing wireless-ids

> Ability to detect suspicious activity such as (WEP/WPA/WPS) attack by sniffing the air for wireless packets.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wireless-ids) (version `24.b132071`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S wireless-ids    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/SYWorks/wireless-ids) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[wireless-ids]] · [[BlackArch Tools]]

