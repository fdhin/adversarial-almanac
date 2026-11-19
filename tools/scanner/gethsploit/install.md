---
tool: "gethsploit"
build-system: "scripts"
upstream: "https://github.com/KarmaHostage/gethspoit"
recipe: "blackarch"
source: "https://github.com/KarmaHostage/gethspoit"
license: "custom:unknown"
blackarch-pkgver: "3.144778b"
---

# Installing gethsploit

> Finding Ethereum nodes which are vulnerable to RPC-attacks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gethsploit) (version `3.144778b`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S gethsploit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/KarmaHostage/gethspoit.git
cd gethspoit
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-yaml (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[gethsploit]] · [[BlackArch Tools]]

