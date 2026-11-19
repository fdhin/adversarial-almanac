---
tool: "archivebox"
build-system: "scripts"
upstream: "https://github.com/pirate/ArchiveBox"
recipe: "blackarch"
aur-package: "archivebox"
source: "https://github.com/pirate/ArchiveBox"
license: "MIT"
blackarch-pkgver: "903.59da482"
---

# Installing archivebox

> The open source self-hosted web archive. Takes browser history/bookmarks/Pocket/Pinboard/etc., saves HTML, JS, PDFs, media, and more.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/archivebox) (version `903.59da482`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S archivebox    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/pirate/ArchiveBox) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `chromium`
  - `python`
  - `wget`
  - `yt-dlp`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[archivebox]] · [[BlackArch Tools]]

