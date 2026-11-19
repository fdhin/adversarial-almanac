---
tool: "attacksurfacemapper"
build-system: "scripts"
upstream: "https://github.com/superhedgy/AttackSurfaceMapper"
recipe: "blackarch"
source: "https://github.com/superhedgy/AttackSurfaceMapper"
license: "GPL3"
blackarch-pkgver: "v1.1.r25.g8a402ed"
---

# Installing attacksurfacemapper

> Tool that aims to automate the reconnaissance process.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/attacksurfacemapper) (version `v1.1.r25.g8a402ed`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S attacksurfacemapper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/superhedgy/AttackSurfaceMapper.git
cd AttackSurfaceMapper
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `backoff`, `beautifulsoup4`, `censys`, `colorama`, `dnslib`, `dnspython`, `ipwhois`, `netaddr`, `pymongo`, `pyopenssl`, `requests`, `requests-html`, `selenium`, `shodan`, `tld`, `trans`, `validator-collection`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[attacksurfacemapper]] · [[BlackArch Tools]]

