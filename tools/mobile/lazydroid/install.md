---
tool: "lazydroid"
build-system: "scripts"
upstream: "https://github.com/nccgroup/LazyDroid"
recipe: "blackarch"
source: "https://github.com/nccgroup/LazyDroid"
license: "Apache"
blackarch-pkgver: "25.0f559ec"
---

# Installing lazydroid

> Tool written as a bash script to facilitate some aspects of an Android Assessment

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lazydroid) (version `25.0f559ec`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S lazydroid    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/nccgroup/LazyDroid.git
cd LazyDroid
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `wget`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[lazydroid]] · [[BlackArch Tools]]

