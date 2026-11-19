---
tool: "manspider"
build-system: "scripts"
upstream: "https://github.com/blacklanternsecurity/MANSPIDER"
recipe: "blackarch"
source: "https://github.com/blacklanternsecurity/MANSPIDER"
license: "custom:unknown"
blackarch-pkgver: "144.ee228a4"
---

# Installing manspider

> Spider entire networks for juicy files sitting on SMB shares. Search filenames or file content - regex supported!

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/manspider) (version `144.ee228a4`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S manspider    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/blacklanternsecurity/MANSPIDER
cd MANSPIDER
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pipx`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[manspider]] · [[BlackArch Tools]]

