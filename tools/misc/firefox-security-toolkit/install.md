---
tool: "firefox-security-toolkit"
build-system: "scripts"
upstream: "https://github.com/mazen160/Firefox-Security-Toolkit"
recipe: "blackarch"
source: "https://github.com/mazen160/Firefox-Security-Toolkit"
license: "MIT"
blackarch-pkgver: "17.d356ab8"
---

# Installing firefox-security-toolkit

> A tool that transforms Firefox browsers into a penetration testing suite.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/firefox-security-toolkit) (version `17.d356ab8`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S firefox-security-toolkit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/mazen160/Firefox-Security-Toolkit
cd Firefox-Security-Toolkit
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `firefox`
  - `wget`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[firefox-security-toolkit]] · [[BlackArch Tools]]

