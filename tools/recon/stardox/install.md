---
tool: "stardox"
build-system: "scripts"
upstream: "https://github.com/0xPrateek/Stardox"
recipe: "blackarch"
source: "https://github.com/0xPrateek/Stardox"
license: "GPL3"
blackarch-pkgver: "41.95b0a97"
---

# Installing stardox

> Github stargazers information gathering tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/stardox) (version `41.95b0a97`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S stardox    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/0xPrateek/Stardox.git
cd Stardox
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `lxml`, `requests`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[stardox]] · [[BlackArch Tools]]

