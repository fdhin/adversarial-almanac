---
tool: "ntfs-file-extractor"
build-system: "scripts"
upstream: "https://github.com/jschicht/NtfsFileExtractor"
recipe: "blackarch"
source: "https://github.com/jschicht/NtfsFileExtractor"
license: "CCPL"
blackarch-pkgver: "6.f2b23d72"
---

# Installing ntfs-file-extractor

> Extract files off NTFS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ntfs-file-extractor) (version `6.f2b23d72`, license CCPL).

## Arch / BlackArch

```bash
sudo pacman -S ntfs-file-extractor    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/jschicht/NtfsFileExtractor.git
cd NtfsFileExtractor
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `wine`
  - `wine-mono`
  - `wine_gecko`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[forensic]] · [[ntfs-file-extractor]] · [[BlackArch Tools]]

