---
tool: "weebdns"
build-system: "scripts"
upstream: "https://github.com/WeebSec/weebdns"
recipe: "blackarch"
source: "https://github.com/WeebSec/weebdns"
license: "custom:unknown"
blackarch-pkgver: "14.c01c04f"
---

# Installing weebdns

> DNS Enumeration with Asynchronicity.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/weebdns) (version `14.c01c04f`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S weebdns    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/WeebSec/weebdns) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `aiodns`, `aiohttp`, `asyncio`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[weebdns]] · [[BlackArch Tools]]

