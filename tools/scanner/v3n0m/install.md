---
tool: "v3n0m"
build-system: "scripts"
upstream: "https://github.com/v3n0m-Scanner/V3n0M-Scanner"
recipe: "blackarch"
source: "https://github.com/v3n0m-Scanner/V3n0M-Scanner"
license: "GPL3"
blackarch-pkgver: "536.16b629f"
---

# Installing v3n0m

> Offensive Security Tool for Vulnerability Scanning & Pentesting

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/v3n0m) (version `536.16b629f`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S v3n0m    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/v3n0m-Scanner/V3n0M-Scanner) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `aiohttp`, `asyncio`, `beautifulsoup4`, `dnspython`, `fuzzywuzzy`, `httplib2`, `pyparsing`, `pysocks`, `python-socks`, `requests`, `termcolor`, `tld`, `tqdm`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/applications`, `/usr/share/icons`

Backlinks: [[scanner]] · [[v3n0m]] · [[BlackArch Tools]]

