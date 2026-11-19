---
tool: "hashtag"
build-system: "scripts"
upstream: "https://github.com/SmeegeSec/HashTag"
recipe: "blackarch"
source: "https://github.com/SmeegeSec/HashTag"
license: "custom:unknown"
blackarch-pkgver: "0.41"
---

# Installing hashtag

> A python script written to parse and identify password hashes.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hashtag) (version `0.41`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S hashtag    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/SmeegeSec/HashTag.git
cd HashTag
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[hashtag]] · [[BlackArch Tools]]

