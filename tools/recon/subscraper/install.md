---
tool: "subscraper"
build-system: "scripts"
upstream: "https://github.com/m8r0wn/subscraper"
recipe: "blackarch"
source: "https://github.com/m8r0wn/subscraper"
license: "GPL3"
blackarch-pkgver: "34.29aa5cc"
---

# Installing subscraper

> Tool that performs subdomain enumeration through various techniques.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/subscraper) (version `34.29aa5cc`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S subscraper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/m8r0wn/subscraper) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `dnspython`, `ipparser`, `requests`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[subscraper]] · [[BlackArch Tools]]

