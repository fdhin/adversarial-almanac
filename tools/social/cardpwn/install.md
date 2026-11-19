---
tool: "cardpwn"
build-system: "scripts"
upstream: "https://github.com/itsmehacker/CardPwn"
recipe: "blackarch"
source: "https://github.com/itsmehacker/CardPwn"
license: "MIT"
blackarch-pkgver: "32.166abf9"
---

# Installing cardpwn

> OSINT Tool to find Breached Credit Cards Information.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cardpwn) (version `32.166abf9`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S cardpwn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/itsmehacker/CardPwn.git
cd CardPwn
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `google`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[cardpwn]] · [[BlackArch Tools]]

