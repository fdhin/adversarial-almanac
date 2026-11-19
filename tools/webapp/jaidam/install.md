---
tool: "jaidam"
build-system: "scripts"
upstream: "https://github.com/stasinopoulos/jaidam"
recipe: "blackarch"
source: "https://github.com/stasinopoulos/jaidam"
license: "custom:unknown"
blackarch-pkgver: "18.15e0fec"
---

# Installing jaidam

> Penetration testing tool that would take as input a list of domain names, scan them, determine if wordpress or joomla platform was used and finally check them automatically, for web vulnerabilities using two well-known open source tools,

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jaidam) (version `18.15e0fec`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S jaidam    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/stasinopoulos/jaidam) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[jaidam]] · [[BlackArch Tools]]

