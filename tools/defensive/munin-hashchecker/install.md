---
tool: "munin-hashchecker"
build-system: "scripts"
upstream: "https://github.com/Neo23x0/munin"
recipe: "blackarch"
source: "https://github.com/Neo23x0/munin"
license: "Apache"
blackarch-pkgver: "260.cf6cbe9"
---

# Installing munin-hashchecker

> Online hash checker for Virustotal and other services

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/munin-hashchecker) (version `260.cf6cbe9`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S munin-hashchecker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Neo23x0/munin) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `cfscrape`, `colorama`, `dnspython`, `flask`, `flask-caching`, `future`, `ipy`, `pycurl`, `pymisp`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[munin-hashchecker]] · [[BlackArch Tools]]

