---
tool: "opendoor"
build-system: "scripts"
upstream: "https://github.com/stanislav-web/OpenDoor"
recipe: "blackarch"
aur-package: "opendoor"
source: "https://github.com/stanislav-web/OpenDoor"
license: "GPL-3.0-or-later"
blackarch-pkgver: "5.13.2"
---

# Installing opendoor

> OWASP WEB Directory Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/opendoor) (version `5.13.2`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S opendoor    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/stanislav-web/OpenDoor.git
cd OpenDoor
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `packaging`, `pysocks`, `setuptools`, `six`, `tabulate`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[opendoor]] · [[BlackArch Tools]]

