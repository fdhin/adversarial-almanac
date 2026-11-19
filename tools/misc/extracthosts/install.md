---
tool: "extracthosts"
build-system: "scripts"
upstream: "https://github.com/bwall/ExtractHosts"
recipe: "blackarch"
source: "https://github.com/bwall/ExtractHosts"
license: "MIT"
blackarch-pkgver: "17.8fdff9e"
---

# Installing extracthosts

> Extracts hosts (IP/Hostnames) from files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/extracthosts) (version `17.8fdff9e`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S extracthosts    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/bwall/ExtractHosts.git
cd ExtractHosts
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[extracthosts]] · [[BlackArch Tools]]

