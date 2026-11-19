---
tool: "extractusnjrnl"
build-system: "scripts"
upstream: "https://github.com/jschicht/ExtractUsnJrnl"
recipe: "blackarch"
source: "https://github.com/jschicht/ExtractUsnJrnl"
license: "CCPL"
blackarch-pkgver: "7.362d4290"
---

# Installing extractusnjrnl

> Tool to extract the $UsnJrnl from an NTFS volume.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/extractusnjrnl) (version `7.362d4290`, license CCPL).

## Arch / BlackArch

```bash
sudo pacman -S extractusnjrnl    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/jschicht/ExtractUsnJrnl.git
cd ExtractUsnJrnl
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

Backlinks: [[forensic]] · [[extractusnjrnl]] · [[BlackArch Tools]]

