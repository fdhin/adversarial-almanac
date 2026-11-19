---
tool: "uncaptcha2"
build-system: "scripts"
upstream: "http://uncaptcha.cs.umd.edu/"
recipe: "blackarch"
source: "http://uncaptcha.cs.umd.edu/"
license: "custom:unknown"
blackarch-pkgver: "7.473f33d"
---

# Installing uncaptcha2

> Defeating the latest version of ReCaptcha with 91% accuracy.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/uncaptcha2) (version `7.473f33d`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S uncaptcha2    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://uncaptcha.cs.umd.edu/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pyautogui`, `speechrecognition`, `xlib`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[uncaptcha2]] · [[BlackArch Tools]]

