---
tool: "iheartxor"
build-system: "scripts"
upstream: "http://hooked-on-mnemonics.blogspot.com.es/p/iheartxor.html"
recipe: "blackarch"
source: "http://codepad.org/TPrsNVU0/raw.py"
license: "custom:unknown"
blackarch-pkgver: "0.01"
---

# Installing iheartxor

> A tool for bruteforcing encoded strings within a boundary defined by a regular expression. It will bruteforce the key value range of 0x1 through 0x255.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/iheartxor) (version `0.01`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S iheartxor    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://hooked-on-mnemonics.blogspot.com.es/p/iheartxor.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[iheartxor]] · [[BlackArch Tools]]

