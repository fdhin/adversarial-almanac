---
tool: "findmyiphone"
build-system: "scripts"
upstream: "https://github.com/manwhoami/findmyiphone"
recipe: "blackarch"
source: "https://github.com/manwhoami/findmyiphone"
license: "custom:unknown"
blackarch-pkgver: "19.aef3ac8"
---

# Installing findmyiphone

> Locates all devices associated with an iCloud account

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/findmyiphone) (version `19.aef3ac8`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S findmyiphone    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/manwhoami/FindMyiPhone.git
cd FindMyiPhone
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[findmyiphone]] · [[BlackArch Tools]]

