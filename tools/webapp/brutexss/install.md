---
tool: "brutexss"
build-system: "scripts"
upstream: "https://github.com/shawarkhanethicalhacker/BruteXSS"
recipe: "blackarch"
source: "https://github.com/shawarkhanethicalhacker/BruteXSS"
license: "GPL3"
blackarch-pkgver: "54.ba753df"
---

# Installing brutexss

> Cross-Site Scripting Bruteforcer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/brutexss) (version `54.ba753df`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S brutexss    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/shawarkhanethicalhacker/BruteXSS.git
cd BruteXSS
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-lxml (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[brutexss]] · [[BlackArch Tools]]

