---
tool: "bingoo"
build-system: "scripts"
upstream: "https://github.com/Hood3dRob1n/BinGoo"
recipe: "blackarch"
aur-package: "bingoo"
source: "https://github.com/Hood3dRob1n/BinGoo"
license: "custom:unknown"
blackarch-pkgver: "3.698132f"
---

# Installing bingoo

> A Linux bash based Bing and Google Dorking Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bingoo) (version `3.698132f`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S bingoo    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Hood3dRob1n/BinGoo.git
cd BinGoo
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `curl`
  - `lynx`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/bingoo`

Backlinks: [[scanner]] · [[bingoo]] · [[BlackArch Tools]]

