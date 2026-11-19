---
tool: "hash-buster"
build-system: "scripts"
upstream: "https://github.com/UltimateHackers/Hash-Buster/"
recipe: "blackarch"
source: "https://github.com/UltimateHackers/Hash-Buster/"
license: "MIT"
blackarch-pkgver: "50.befb02a"
---

# Installing hash-buster

> A python script which scraps online hash crackers to find cleartext of a hash.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hash-buster) (version `50.befb02a`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S hash-buster    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/UltimateHackers/Hash-Buster.git
cd Hash-Buster
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[hash-buster]] · [[BlackArch Tools]]

