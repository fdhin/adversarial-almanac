---
tool: "phonesploit"
build-system: "scripts"
upstream: "https://github.com/metachar/PhoneSploit"
recipe: "blackarch"
source: "https://github.com/metachar/PhoneSploit"
license: "custom:unknown"
blackarch-pkgver: "51.0193f9e"
---

# Installing phonesploit

> Adb exploiting tools.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/phonesploit) (version `51.0193f9e`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S phonesploit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/metachar/PhoneSploit) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `android-tools`
  - `python2`
- ⚠️ Python 2 only deps: python2-colorama (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[phonesploit]] · [[BlackArch Tools]]

