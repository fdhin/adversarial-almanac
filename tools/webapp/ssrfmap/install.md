---
tool: "ssrfmap"
build-system: "scripts"
upstream: "https://github.com/swisskyrepo/SSRFmap"
recipe: "blackarch"
source: "https://github.com/swisskyrepo/SSRFmap"
license: "MIT"
blackarch-pkgver: "127.290e07d"
---

# Installing ssrfmap

> Automatic SSRF fuzzer and exploitation tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ssrfmap) (version `127.290e07d`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S ssrfmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/swisskyrepo/SSRFmap.git
cd SSRFmap
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `flask`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[ssrfmap]] · [[BlackArch Tools]]

