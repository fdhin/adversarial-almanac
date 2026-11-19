---
tool: "rpivot"
build-system: "scripts"
upstream: "https://github.com/artkond/rpivot"
recipe: "blackarch"
source: "https://github.com/artkond/rpivot"
license: "custom:unknown"
blackarch-pkgver: "5.4963487"
---

# Installing rpivot

> Socks4 reverse proxy for penetration testing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rpivot) (version `5.4963487`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S rpivot    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/artkond/rpivot) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-ntlm-auth (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[proxy]] · [[rpivot]] · [[BlackArch Tools]]

