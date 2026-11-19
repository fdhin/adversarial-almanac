---
tool: "gasmask"
build-system: "scripts"
upstream: "https://github.com/twelvesec/gasmask"
recipe: "blackarch"
source: "https://github.com/twelvesec/gasmask"
license: "GPL3"
blackarch-pkgver: "172.2527371"
---

# Installing gasmask

> All in one Information gathering tool - OSINT.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gasmask) (version `172.2527371`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S gasmask    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/twelvesec/gasmask) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `censys`, `dnspython`, `python-whois`, `requests`, `shodan`, `validators`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[gasmask]] · [[BlackArch Tools]]

