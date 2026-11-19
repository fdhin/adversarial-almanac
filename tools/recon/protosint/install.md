---
tool: "protosint"
build-system: "scripts"
upstream: "https://github.com/pixelbubble/ProtOSINT"
recipe: "blackarch"
source: "https://github.com/pixelbubble/ProtOSINT"
license: "MIT"
blackarch-pkgver: "35.34e570a"
---

# Installing protosint

> Python script that helps you investigate Protonmail accounts and ProtonVPN IP addresses.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/protosint) (version `35.34e570a`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S protosint    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/pixelbubble/ProtOSINT.git
cd ProtOSINT
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[protosint]] · [[BlackArch Tools]]

