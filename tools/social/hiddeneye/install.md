---
tool: "hiddeneye"
build-system: "scripts"
upstream: "https://github.com/DarkSecDevelopers/HiddenEye"
recipe: "blackarch"
source: "https://github.com/DarkSecDevelopers/HiddenEye"
license: "MIT"
blackarch-pkgver: "1228.7a3deaf"
---

# Installing hiddeneye

> Modern phishing tool with advanced functionality.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hiddeneye) (version `1228.7a3deaf`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S hiddeneye    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/DarkSecDevelopers/HiddenEye.git
cd HiddenEye
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `future`, `pgrep`, `pyngrok`, `requests`, `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `php`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[hiddeneye]] · [[BlackArch Tools]]

