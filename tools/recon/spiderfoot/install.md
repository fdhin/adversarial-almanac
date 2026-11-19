---
tool: "spiderfoot"
build-system: "scripts"
upstream: "https://github.com/smicallef/spiderfoot"
recipe: "blackarch"
aur-package: "spiderfoot"
source: "https://github.com/smicallef/spiderfoot"
license: "GPL2"
blackarch-pkgver: "4.0"
kali-package: "spiderfoot"
kali-version: "4.0-0kali5"
---

# Installing spiderfoot

> The Open Source Footprinting Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/spiderfoot) (version `4.0`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S spiderfoot    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install spiderfoot    # Kali package version 4.0-0kali5
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/smicallef/spiderfoot) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `adblockparser`, `beautifulsoup4`, `cherrypy`, `cherrypy-cors`, `cryptography`, `dnspython`, `exifread`, `future`, `httpagentparser`, `ipaddr`, `ipwhois`, `lxml`, `mako`, `netaddr`, `networkx`, `openpyxl`, `phonenumbers`, `publicsuffixlist`, `pygexf`, `pyopenssl`, `pypdf2`, `pysocks`, `python-docx`, `python-pptx`, `python-whois`, `requests`, `secure`, `stem`, `yaml`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[spiderfoot]] · [[BlackArch Tools]]

