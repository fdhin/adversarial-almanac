---
tool: "dsstore-crawler"
build-system: "scripts"
upstream: "https://github.com/anantshri/DS_Store_crawler_parser"
recipe: "blackarch"
source: "https://github.com/anantshri/DS_Store_crawler_parser"
license: "Apache-2.0"
blackarch-pkgver: "7.efa51f5"
---

# Installing dsstore-crawler

> A parser + crawler for .DS_Store files exposed publically.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dsstore-crawler) (version `7.efa51f5`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S dsstore-crawler    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/anantshri/DS_Store_crawler_parser.git
cd DS_Store_crawler_parser
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `ds-store`, `mac-alias`, `requests`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[dsstore-crawler]] · [[BlackArch Tools]]

