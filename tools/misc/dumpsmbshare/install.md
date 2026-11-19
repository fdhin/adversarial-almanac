---
tool: "dumpsmbshare"
build-system: "scripts"
upstream: "https://github.com/p0dalirius/DumpSMBShare"
recipe: "blackarch"
source: "https://github.com/p0dalirius/DumpSMBShare"
license: "custom:unknown"
blackarch-pkgver: "23.1e5ceb1"
---

# Installing dumpsmbshare

> A script to dump files and folders remotely from a Windows SMB share.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dumpsmbshare) (version `23.1e5ceb1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dumpsmbshare    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/p0dalirius/DumpSMBShare.git
cd DumpSMBShare
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[dumpsmbshare]] · [[BlackArch Tools]]

