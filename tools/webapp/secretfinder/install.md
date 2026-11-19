---
tool: "secretfinder"
build-system: "scripts"
upstream: "https://github.com/m4ll0k/SecretFinder"
recipe: "blackarch"
source: "https://github.com/m4ll0k/SecretFinder"
license: "GPL-3.0-or-later"
blackarch-pkgver: "15.d06119d"
---

# Installing secretfinder

> A python script to find sensitive data (apikeys, accesstoken, jwt,..) in javascript files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/secretfinder) (version `15.d06119d`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S secretfinder    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/m4ll0k/SecretFinder.git
cd SecretFinder
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `jsbeautifier`, `lxml`, `requests`, `requests-file`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[secretfinder]] · [[BlackArch Tools]]

