---
tool: "phpsploit"
build-system: "scripts"
upstream: "https://github.com/nil0x42/phpsploit"
recipe: "blackarch"
source: "https://github.com/nil0x42/phpsploit"
license: "GPL3"
blackarch-pkgver: "1021.aea961d"
---

# Installing phpsploit

> Stealth post-exploitation framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/phpsploit) (version `1021.aea961d`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S phpsploit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/nil0x42/phpsploit) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `phpserialize`, `pygments`, `pyparsing`, `termcolor`, `tornado`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[phpsploit]] · [[BlackArch Tools]]

