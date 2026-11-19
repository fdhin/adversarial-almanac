---
tool: "crosslinked"
build-system: "scripts"
upstream: "https://github.com/m8r0wn/crosslinked"
recipe: "blackarch"
source: "https://github.com/m8r0wn/crosslinked"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v0.3.0.r23.g2ae8d7b"
---

# Installing crosslinked

> LinkedIn enumeration tool to extract valid employee names from an organization through search engine scraping.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/crosslinked) (version `v0.3.0.r23.g2ae8d7b`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S crosslinked    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/m8r0wn/crosslinked) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `requests`, `taser`, `unidecode`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[crosslinked]] · [[BlackArch Tools]]

