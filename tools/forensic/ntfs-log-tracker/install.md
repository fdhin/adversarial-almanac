---
tool: "ntfs-log-tracker"
build-system: "scripts"
upstream: "https://sites.google.com/site/forensicnote/ntfs-log-tracker"
recipe: "blackarch"
source: "https://sites.google.com/site/forensicnote/ntfs-log-tracker"
license: "custom:unknown"
blackarch-pkgver: "1.6"
---

# Installing ntfs-log-tracker

> This tool can parse $LogFile, $UsnJrnl of NTFS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ntfs-log-tracker) (version `1.6`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ntfs-log-tracker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://sites.google.com/site/forensicnote/ntfs-log-tracker) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `wine`
  - `wine-mono`
  - `wine_gecko`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[forensic]] · [[ntfs-log-tracker]] · [[BlackArch Tools]]

