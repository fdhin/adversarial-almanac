---
tool: "finalrecon"
build-system: "scripts"
upstream: "https://github.com/thewhiteh4t/FinalRecon"
recipe: "blackarch"
source: "https://github.com/thewhiteh4t/FinalRecon"
license: "MIT"
blackarch-pkgver: "v1.1.8.r6.g98a0bac"
---

# Installing finalrecon

> OSINT Tool for All-In-One Web Reconnaissance.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/finalrecon) (version `v1.1.8.r6.g98a0bac`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S finalrecon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/thewhiteh4t/FinalRecon.git
cd FinalRecon
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `aiodns`, `aiohttp`, `beautifulsoup4`, `dnslib`, `icmplib`, `ipwhois`, `lxml`, `psycopg2`, `requests`, `tldextract`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[finalrecon]] · [[BlackArch Tools]]

