---
tool: "facebookosint"
build-system: "scripts"
upstream: "https://github.com/tomoneill19/facebookOSINT"
recipe: "blackarch"
source: "https://github.com/tomoneill19/facebookOSINT"
license: "GPL"
blackarch-pkgver: "22.3fb03f5"
---

# Installing facebookosint

> OSINT tool to replace facebook graph search.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/facebookosint) (version `22.3fb03f5`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S facebookosint    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/tomoneill19/FacebookOSINT.git
cd FacebookOSINT
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[facebookosint]] · [[BlackArch Tools]]

