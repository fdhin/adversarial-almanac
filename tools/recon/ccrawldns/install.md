---
tool: "ccrawldns"
build-system: "scripts"
upstream: "https://github.com/lgandx/CCrawlDNS"
recipe: "blackarch"
source: "https://github.com/lgandx/CCrawlDNS"
license: "GPL-1.0-or-later"
blackarch-pkgver: "v1.0.r2.gdfd5062"
---

# Installing ccrawldns

> Retrieves from the CommonCrawl data set unique subdomains for a given domain name.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ccrawldns) (version `v1.0.r2.gdfd5062`, license GPL-1.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S ccrawldns    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/lgandx/CCrawlDNS.git
cd CCrawlDNS
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[ccrawldns]] · [[BlackArch Tools]]

