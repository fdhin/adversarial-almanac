---
tool: "masscan-automation"
build-system: "scripts"
upstream: "https://github.com/trevordavenport/MasscanAutomation"
recipe: "blackarch"
source: "https://github.com/trevordavenport/MasscanAutomation"
license: "custom:unknown"
blackarch-pkgver: "26.a170abc"
---

# Installing masscan-automation

> Masscan integrated with Shodan API.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/masscan-automation) (version `26.a170abc`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S masscan-automation    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/trevordavenport/MasscanAutomation.git
cd MasscanAutomation
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `masscan`
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-requests, python2-shodan (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[masscan-automation]] · [[BlackArch Tools]]

