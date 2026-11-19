---
tool: "bluphish"
build-system: "scripts"
upstream: "https://github.com/olivo/BluPhish"
recipe: "blackarch"
source: "https://github.com/olivo/BluPhish"
license: "Apache2"
blackarch-pkgver: "9.a7200bd"
---

# Installing bluphish

> Bluetooth device and service discovery tool that can be used for security assessment and penetration testing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bluphish) (version `9.a7200bd`, license Apache2).

## Arch / BlackArch

```bash
sudo pacman -S bluphish    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/olivo/BluPhish.git
cd BluPhish
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[bluetooth]] · [[bluphish]] · [[BlackArch Tools]]

