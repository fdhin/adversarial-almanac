---
tool: "urlextractor"
build-system: "scripts"
upstream: "https://github.com/eschultze/URLextractor"
recipe: "blackarch"
source: "https://github.com/eschultze/URLextractor"
license: "MIT"
blackarch-pkgver: "19.739864d"
---

# Installing urlextractor

> Information gathering & website reconnaissance.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/urlextractor) (version `19.739864d`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S urlextractor    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/eschultze/URLextractor.git
cd URLextractor
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `tldextract`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `bc`
  - `bind-tools`
  - `whois`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[urlextractor]] · [[BlackArch Tools]]

