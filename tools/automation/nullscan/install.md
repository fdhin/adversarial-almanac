---
tool: "nullscan"
build-system: "scripts"
upstream: "http://www.nullsecurity.net/tools/automation.html"
recipe: "blackarch"
source: "http://www.nullsecurity.net/tools/automation.html"
license: "MIT"
blackarch-pkgver: "1.0.1"
---

# Installing nullscan

> A modular framework designed to chain and automate security tests.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nullscan) (version `1.0.1`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S nullscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.nullsecurity.net/tools/automation.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `colorama`, `dnspython`, `get-mac`, `google`, `ipwhois`, `netaddr`, `netifaces`, `psutil`, `python-libnmap`, `python-whois`, `requests`
- **System libraries** (Arch name — translate for your distro):
  - `nmap`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[automation]] · [[nullscan]] · [[BlackArch Tools]]

