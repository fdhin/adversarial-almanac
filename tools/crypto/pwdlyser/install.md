---
tool: "pwdlyser"
build-system: "scripts"
upstream: "https://github.com/ins1gn1a/pwdlyser"
recipe: "blackarch"
source: "https://github.com/ins1gn1a/pwdlyser"
license: "MIT"
blackarch-pkgver: "v1.0.0.r8.gb84c635"
---

# Installing pwdlyser

> Python-based CLI Password Analyser (Reporting Tool).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pwdlyser) (version `v1.0.0.r8.gb84c635`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pwdlyser    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ins1gn1a/pwdlyser) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[pwdlyser]] · [[BlackArch Tools]]

