---
tool: "littlebrother"
build-system: "scripts"
upstream: "https://github.com/lulz3xploit/LittleBrother"
recipe: "blackarch"
source: "https://github.com/lulz3xploit/LittleBrother"
license: "custom:unknown"
blackarch-pkgver: "112.338cf82"
---

# Installing littlebrother

> OSINT tool to get informations on French, Belgian and Swizerland people.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/littlebrother) (version `112.338cf82`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S littlebrother    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/lulz3xploit/LittleBrother.git
cd LittleBrother
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `colorama`, `exifread`, `requests`, `terminaltables`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[littlebrother]] · [[BlackArch Tools]]

