---
tool: "catana"
build-system: "scripts"
upstream: "https://github.com/D3vil0p3r/catana"
recipe: "blackarch"
source: "https://github.com/D3vil0p3r/catana"
license: "GPL3"
blackarch-pkgver: "31.9ea1f0d"
---

# Installing catana

> Filter your wordlist according to the specified password policy.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/catana) (version `31.9ea1f0d`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S catana    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/D3vil0p3r/catana) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[catana]] · [[BlackArch Tools]]

