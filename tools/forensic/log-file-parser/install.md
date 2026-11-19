---
tool: "log-file-parser"
build-system: "scripts"
upstream: "https://github.com/jschicht/LogFileParser"
recipe: "blackarch"
source: "https://github.com/jschicht/LogFileParser"
license: "CCPL"
blackarch-pkgver: "60.c7a0ae7e"
---

# Installing log-file-parser

> Parser for $LogFile on NTFS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/log-file-parser) (version `60.c7a0ae7e`, license CCPL).

## Arch / BlackArch

```bash
sudo pacman -S log-file-parser    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/jschicht/LogFileParser.git
cd LogFileParser
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

Backlinks: [[forensic]] · [[log-file-parser]] · [[BlackArch Tools]]

