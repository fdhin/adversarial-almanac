---
tool: "cr3dov3r"
build-system: "scripts"
upstream: "https://github.com/D4Vinci/Cr3dOv3r"
recipe: "blackarch"
source: "https://github.com/D4Vinci/Cr3dOv3r"
license: "MIT"
blackarch-pkgver: "48.49bbdbe"
---

# Installing cr3dov3r

> Search for public leaks for email addresses + check creds against 16 websites.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cr3dov3r) (version `48.49bbdbe`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S cr3dov3r    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/D4Vinci/Cr3dOv3r.git
cd Cr3dOv3r
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `mechanicalsoup`, `pyopenssl`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[cr3dov3r]] · [[BlackArch Tools]]

