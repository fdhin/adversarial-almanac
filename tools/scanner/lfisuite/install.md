---
tool: "lfisuite"
build-system: "scripts"
upstream: "https://github.com/D35m0nd142/LFISuite"
recipe: "blackarch"
source: "https://github.com/D35m0nd142/LFISuite"
license: "GPL3"
blackarch-pkgver: "85.470e01f"
---

# Installing lfisuite

> Totally Automatic LFI Exploiter (+ Reverse Shell) and Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lfisuite) (version `85.470e01f`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S lfisuite    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/D35m0nd142/LFISuite.git
cd LFISuite
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[lfisuite]] · [[BlackArch Tools]]

