---
tool: "backdoorppt"
build-system: "scripts"
upstream: "https://github.com/r00t-3xp10it/backdoorppt"
recipe: "blackarch"
source: "https://github.com/r00t-3xp10it/backdoorppt"
license: "custom:unknown"
blackarch-pkgver: "88.d0e7f91"
---

# Installing backdoorppt

> Transform your payload.exe into one fake word doc (.ppt).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/backdoorppt) (version `88.d0e7f91`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S backdoorppt    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/r00txp10it/backdoorppt) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `ruby`
  - `wine`
  - `xterm`
  - `zenity`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[backdoorppt]] · [[BlackArch Tools]]

