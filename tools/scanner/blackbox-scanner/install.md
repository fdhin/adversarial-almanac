---
tool: "blackbox-scanner"
build-system: "scripts"
upstream: "https://github.com/sepehrdaddev/blackbox"
recipe: "blackarch"
source: "https://github.com/sepehrdaddev/blackbox"
license: "custom:unknown"
blackarch-pkgver: "1.7a25220"
---

# Installing blackbox-scanner

> Dork scanner & bruteforcing & hash cracker with blackbox framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/blackbox-scanner) (version `1.7a25220`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S blackbox-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/sepehrdaddev/blackbox.git
cd blackbox
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-passlib, python2-pexpect, python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[blackbox-scanner]] · [[BlackArch Tools]]

