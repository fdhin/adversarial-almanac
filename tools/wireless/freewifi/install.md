---
tool: "freewifi"
build-system: "scripts"
upstream: "https://github.com/kylemcdonald/FreeWifi"
recipe: "blackarch"
source: "https://github.com/kylemcdonald/FreeWifi"
license: "MIT"
blackarch-pkgver: "30.1cb752b"
---

# Installing freewifi

> How to get free wifi.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/freewifi) (version `30.1cb752b`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S freewifi    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/kylemcdonald/FreeWifi.git
cd FreeWifi
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `netaddr`, `netifaces`, `spoofmac`, `tqdm`, `wireless`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[freewifi]] · [[BlackArch Tools]]

