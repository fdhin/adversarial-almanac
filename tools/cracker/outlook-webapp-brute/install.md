---
tool: "outlook-webapp-brute"
build-system: "scripts"
upstream: "https://github.com/lijiejie/OutLook_WebAPP_Brute"
recipe: "blackarch"
source: "https://github.com/lijiejie/OutLook_WebAPP_Brute"
license: "custom:unknown"
blackarch-pkgver: "1.61d7177"
---

# Installing outlook-webapp-brute

> Microsoft Outlook WebAPP Brute.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/outlook-webapp-brute) (version `1.61d7177`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S outlook-webapp-brute    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/lijiejie/OutLook_WebAPP_Brute.git
cd OutLook_WebAPP_Brute
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[outlook-webapp-brute]] · [[BlackArch Tools]]

