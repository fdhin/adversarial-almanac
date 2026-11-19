---
tool: "pastemonitor"
build-system: "scripts"
upstream: "https://github.com/pixelbubble/PasteMonitor"
recipe: "blackarch"
source: "https://github.com/pixelbubble/PasteMonitor"
license: "MIT"
blackarch-pkgver: "12.bc5d326"
---

# Installing pastemonitor

> Scrape Pastebin API to collect daily pastes, setup a wordlist and be alerted by email when you have a match..

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pastemonitor) (version `12.bc5d326`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pastemonitor    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/pixelbubble/PasteMonitor.git
cd PasteMonitor
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[pastemonitor]] · [[BlackArch Tools]]

