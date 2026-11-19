---
tool: "jsonbee"
build-system: "scripts"
upstream: "https://github.com/zigoo0/JSONBee"
recipe: "blackarch"
source: "https://github.com/zigoo0/JSONBee"
license: "GPL3"
blackarch-pkgver: "30.c0c87fc"
---

# Installing jsonbee

> A ready to use JSONP endpoints/payloads to help bypass content security policy (CSP).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jsonbee) (version `30.c0c87fc`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S jsonbee    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/zigoo0/JSONBee.git
cd JSONBee
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `php`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[jsonbee]] · [[BlackArch Tools]]

