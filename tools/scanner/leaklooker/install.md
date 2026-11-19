---
tool: "leaklooker"
build-system: "scripts"
upstream: "https://github.com/woj-ciech/LeakLooker"
recipe: "blackarch"
source: "https://github.com/woj-ciech/LeakLooker"
license: "custom:unknown"
blackarch-pkgver: "5.0d2b9fc"
---

# Installing leaklooker

> Find open databases with Shodan.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/leaklooker) (version `5.0d2b9fc`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S leaklooker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/woj-ciech/LeakLooker.git
cd LeakLooker
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `hurry-filesize`, `shodan`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[leaklooker]] · [[BlackArch Tools]]

