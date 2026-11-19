---
tool: "swap-digger"
build-system: "scripts"
upstream: "https://github.com/sevagas/swap_digger"
recipe: "blackarch"
source: "https://github.com/sevagas/swap_digger"
license: "GPL3"
blackarch-pkgver: "51.4d18ce0"
---

# Installing swap-digger

> A tool used to automate Linux swap analysis during post-exploitation or forensics.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/swap-digger) (version `51.4d18ce0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S swap-digger    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/sevagas/swap_digger.git
cd swap_digger
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[swap-digger]] · [[BlackArch Tools]]

