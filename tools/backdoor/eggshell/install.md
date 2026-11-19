---
tool: "eggshell"
build-system: "scripts"
upstream: "https://github.com/neoneggplant/EggShell"
recipe: "blackarch"
source: "https://github.com/neoneggplant/EggShell"
license: "GPL2"
blackarch-pkgver: "157.eaeeea7"
---

# Installing eggshell

> iOS/macOS/Linux Remote Administration Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/eggshell) (version `157.eaeeea7`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S eggshell    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/neoneggplant/EggShell.git
cd EggShell
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[eggshell]] · [[BlackArch Tools]]

