---
tool: "hiddeneye-legacy"
build-system: "scripts"
upstream: "https://github.com/DarkSecDevelopers/HiddenEye-Legacy"
recipe: "blackarch"
source: "https://github.com/DarkSecDevelopers/HiddenEye-Legacy"
license: "GPL3"
blackarch-pkgver: "RC1.r226.g7a3deaf"
---

# Installing hiddeneye-legacy

> Modern Phishing Tool With Advanced Functionality.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hiddeneye-legacy) (version `RC1.r226.g7a3deaf`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S hiddeneye-legacy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/DarkSecDevelopers/HiddenEye-Legacy) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `certifi`, `chardet`, `future`, `idna`, `kill`, `pgrep`, `pyngrok`, `requests`, `urllib3`, `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[hiddeneye-legacy]] · [[BlackArch Tools]]

