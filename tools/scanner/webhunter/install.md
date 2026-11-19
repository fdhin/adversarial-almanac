---
tool: "webhunter"
build-system: "scripts"
upstream: "https://github.com/peedcorp/WebHunter"
recipe: "blackarch"
source: "https://github.com/peedcorp/WebHunter"
license: "GPL3"
blackarch-pkgver: "12.918b606"
---

# Installing webhunter

> Tool for scanning web applications and networks and easily completing the process of collecting knowledge.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/webhunter) (version `12.918b606`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S webhunter    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/peedcorp/WebHunter.git
cd WebHunter
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[webhunter]] · [[BlackArch Tools]]

