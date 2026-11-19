---
tool: "lfifreak"
build-system: "scripts"
upstream: "https://github.com/OsandaMalith/LFiFreak/"
recipe: "blackarch"
source: "https://github.com/OsandaMalith/LFiFreak/"
license: "custom:unknown"
blackarch-pkgver: "22.86b97dd"
---

# Installing lfifreak

> A unique automated LFi Exploiter with Bind/Reverse Shells.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lfifreak) (version `22.86b97dd`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S lfifreak    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/OsandaMalith/LFiFreak.git
cd LFiFreak
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[lfifreak]] · [[BlackArch Tools]]

